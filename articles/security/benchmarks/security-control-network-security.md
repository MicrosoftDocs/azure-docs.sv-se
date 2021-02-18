---
title: Azure säkerhets kontroll – nätverks säkerhet
description: Nätverks säkerhet i Azure säkerhets kontroll
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: cc52d1f6eec5f1aedb0db37b3945f7be6d9f62a8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595641"
---
# <a name="security-control-network-security"></a>Säkerhets kontroll: nätverks säkerhet

Nätverks säkerhets rekommendationer fokusera på att ange vilka nätverks protokoll, TCP/UDP-portar och nätverksanslutna tjänster som tillåts eller nekas åtkomst till Azure-tjänster.

## <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 1.1 | 9,2, 9,4, 14,1, 14,2, 14,3 | Kund |

Se till att alla distributioner av Virtual Network undernät har en nätverks säkerhets grupp som tillämpas med nätverks åtkomst kontroller som är specifika för programmets betrodda portar och källor. När det är tillgängligt använder du privata slut punkter med privat länk för att skydda dina Azure-tjänsteresurser till ditt virtuella nätverk genom att utöka VNet-identiteten till tjänsten. Använd tjänstens slut punkter när privata slut punkter och privat länk inte är tillgänglig. Information om tjänstspecifika krav finns i säkerhets rekommendationer för den aktuella tjänsten. 

Om du har ett speciellt användnings fall kan kravet uppfyllas genom att implementera Azure-brandväggen.

- [Förstå Virtual Network tjänstens slut punkter](../../virtual-network/virtual-network-service-endpoints-overview.md)

- [Förstå privat Azure-länk](../../private-link/private-link-overview.md)

- [Så här skapar du en Virtual Network](../../virtual-network/quick-create-portal.md)

- [Så här skapar du en NSG med en säkerhets konfiguration](../../virtual-network/tutorial-filter-network-traffic.md)

- [Distribuera och konfigurera Azure-brandvägg](../../firewall/tutorial-firewall-deploy-portal.md)

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverkskort

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 1.2 | 9,3, 12,2, 12,8 | Kund |

Använd Azure Security Center och följ rekommendationerna för nätverks skydd för att skydda dina nätverks resurser i Azure. Aktivera NSG Flow-loggar och skicka loggar till ett lagrings konto för trafik granskning. Du kan också skicka NSG Flow-loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflöde i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

- [Så här aktiverar du NSG Flow-loggar](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Trafikanalys](../../network-watcher/traffic-analytics.md)

- [Förstå nätverks säkerhet som tillhandahålls av Azure Security Center](../../security-center/security-center-network-recommendations.md)

## <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 1.3 | 9,5 | Kund |

Distribuera Azure Web Application Firewall (WAF) framför viktiga webb program för ytterligare inspektion av inkommande trafik. Aktivera diagnostikinställningar för WAF och mata in loggar till ett lagrings konto, en Event Hub-eller Log Analytics-arbetsyta.

- [Så här distribuerar du Azure-WAF](../../web-application-firewall/ag/create-waf-policy-ag.md)

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 1.4 | 12,3 | Kund |

Aktivera DDoS standard skydd på dina virtuella Azure-nätverk för att skydda mot DDoS-attacker. Använd Azure Security Center integrerad Hot information för att neka kommunikation med kända skadliga IP-adresser.

Distribuera Azure-brandväggen på var och en av organisationens nätverks gränser med hot information aktive rad och konfigurerad för "varning och neka" för skadlig nätverks trafik.

Använd Azure Security Center just-in-Time Network Access för att konfigurera NSG: er för att begränsa exponering av slut punkter till godkända IP-adresser under en begränsad period.

Använd Azure Security Center anpassad nätverks härdning för att rekommendera NSG-konfigurationer som begränsar portar och käll-IP-adresser baserat på faktisk trafik och hot information.

- [Så här konfigurerar du DDoS-skydd](../../ddos-protection/manage-ddos-protection.md)

- [Så här distribuerar du Azure-brandvägg](../../firewall/tutorial-firewall-deploy-portal.md)

- [Förstå Azure Security Center integrerad Hot information](../../security-center/azure-defender.md)

- [Förstå Azure Security Center anpassad nätverks härdning](../../security-center/security-center-adaptive-network-hardening.md)

- [Förstå Azure Security Center just-in-Time-nätverk Access Control](../../security-center/security-center-just-in-time.md)

## <a name="15-record-network-packets"></a>1,5: registrera nätverks paket

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 1.5 | 12,5 | Kund |

Aktivera Network Watcher paket fångst för att undersöka avvikande aktiviteter.

- [Så här aktiverar du Network Watcher](../../network-watcher/network-watcher-create.md)

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera Network-baserad intrångs identifiering/intrångs skydd system (ID/IP-adresser)

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 1.6 | 12,6, 12,7 | Kund |

Välj ett erbjudande från Azure Marketplace som stöder ID/IP-funktioner med funktioner för nytto Last granskning.  Om intrångs identifiering och/eller skydd som baseras på nytto lasts granskning inte är ett krav kan du använda Azure-brandväggen med hot information. Azure Firewall Threat Intelligence-baserad filtrering kan varna och neka trafik till och från kända skadliga IP-adresser och domäner. IP-adresserna och domänerna hämtas från Microsoft Threat Intelligence-flödet.

Distribuera den brand Väggs lösning som du väljer för var och en av organisationens nätverks gränser för att upptäcka och/eller neka skadlig trafik.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Så här distribuerar du Azure-brandvägg](../../firewall/tutorial-firewall-deploy-portal.md)

- [Konfigurera aviseringar med Azure-brandväggen](../../firewall/threat-intel.md)

## <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 1.7 | 12,9, 12,10 | Kund |

Distribuera Azure Application Gateway för webb program med HTTPS/TLS aktiverat för betrodda certifikat.

- [Så här distribuerar du Application Gateway](../../application-gateway/quick-create-portal.md)

- [Så här konfigurerar du Application Gateway att använda HTTPS](../../application-gateway/create-ssl-portal.md)

- [Förstå belastnings utjämning för Layer 7 med Azure Web Application Gateway](../../application-gateway/overview.md)

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 1.8 | 1.5 | Kund |

Använd Virtual Network Service-taggar för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper eller Azure-brandvägg. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet (t. ex. API Management) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

Du kan också använda program säkerhets grupper för att förenkla komplex säkerhets konfiguration. Med programsäkerhetsgrupper kan du konfigurera nätverkssäkerhet som ett naturligt tillägg till ett programs struktur, så att du kan gruppera virtuella datorer och definiera nätverkssäkerhetsprinciper baserat på dessa grupper.

- [Förstå och använda service märken](../../virtual-network/service-tags-overview.md)

- [Förstå och använda program säkerhets grupper](../../virtual-network/network-security-groups-overview.md#application-security-groups)

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 1.9 | 11,1 | Kund |

Definiera och implementera standardinställda säkerhetskonfigurationer för nätverks resurser med Azure Policy.

Du kan också använda Azure-ritningar för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljö artefakter, till exempel Azure Resources Manager-mallar, Azure RBAC-kontroller och-principer, i en enda skiss definition. Du kan använda skissen för nya prenumerationer och finjustera kontroll och hantering genom versions hantering.

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy exempel för nätverk](../../governance/policy/samples/built-in-policies.md#network)

- [Så här skapar du en Azure Blueprint](../../governance/blueprints/create-blueprint-portal.md)

## <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 1,10 | 11,2 | Kund |

Använd taggar för NSG: er och andra resurser som är relaterade till nätverks säkerhets-och trafikflödet. För enskilda NSG-regler använder du fältet Beskrivning för att ange affärs behov och/eller varaktighet (osv.) för alla regler som tillåter trafik till/från ett nätverk.

Använd någon av de inbyggda Azure Policy definitionerna som är relaterade till taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och meddela dig om befintliga otaggade resurser.

Du kan använda Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

- [Skapa och använda Taggar](../../azure-resource-manager/management/tag-resources.md)

- [Så här skapar du en Virtual Network](../../virtual-network/quick-create-portal.md)

- [Så här skapar du en NSG med en säkerhets konfiguration](../../virtual-network/tutorial-filter-network-traffic.md)

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 1,11 | 11,3 | Kund |

Använd Azure aktivitets logg för att övervaka datorkonfigurationer och identifiera ändringar i dina Azure-resurser. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska resurser sker.

- [Visa och hämta Azure aktivitets logg händelser](../../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](../../azure-monitor/alerts/alerts-activity-log.md)

## <a name="next-steps"></a>Nästa steg

- Se nästa säkerhets kontroll: [loggning och övervakning](security-control-logging-monitoring.md)