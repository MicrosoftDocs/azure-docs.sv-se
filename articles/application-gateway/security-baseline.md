---
title: Azures säkerhets bas linje för Application Gateway
description: Application Gateway säkerhets bas linje ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: application-gateway
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 4fc26d8e154dc0a58bb3436a8161d2e21efa2b86
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952022"
---
# <a name="azure-security-baseline-for-application-gateway"></a>Azures säkerhets bas linje för Application Gateway

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 1,0](../security/benchmarks/overview-v1.md) till Application Gateway. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure.
Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för Application Gateway. **Kontroller** som inte är tillämpliga på Application Gateway har uteslutits.

 
Om du vill se hur Application Gateway helt mappar till Azures säkerhets mätning, se den [fullständiga Application Gateway mappnings filen för säkerhets bas linjen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: kontrol lera att alla Virtual Network Azure Application Gateway-undernätet har en nätverks säkerhets grupp (NSG) som tillämpas med nätverks åtkomst kontroller som är specifika för programmets betrodda portar och källor. Nätverks säkerhets grupper stöds på Azure Application Gateway, men det finns vissa begränsningar och krav som måste följas för att din NSG och Azure Application-Gateway ska fungera som förväntat.

- [Förstå begränsningar och krav för att använda NSG: er med Azure Application Gateway](configuration-overview.md)

- [Så här skapar du en NSG med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: [Azures säkerhets benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) är standard policy initiativ för Security Center och är grunden för [Security Center rekommendationer](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy-definitioner som är relaterade till den här kontrollen aktive ras automatiskt av Security Center. Aviseringar som är relaterade till den här kontrollen kan kräva en [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan för de relaterade tjänsterna.

**Azure policy inbyggda definitioner – Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverks gränssnitt

**Vägledning**: för de nätverks säkerhets grupper (NSG: er) som är associerade med dina Azure Application Gateway-undernät aktiverar du NSG Flow-loggar och skickar loggar till ett lagrings konto för trafik granskning. Du kan också skicka NSG Flow-loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflöde i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

Obs! det finns vissa fall där NSG Flow-loggar som är kopplade till dina Azure Application Gateway-undernät inte visar trafik som har tillåtits. Om konfigurationen matchar följande scenario visas inte tillåten trafik i dina NSG-flödes loggar:

- Du har distribuerat Application Gateway v2
- Du har en NSG på Application Gateway-undernätet
- Du har aktiverat NSG Flow-loggar på den NSG

Mer information finns i referenserna nedan.

- [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

- [Förstå nätverks säkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Vanliga frågor och svar om diagnostik och loggning för Azure Application Gateway](./application-gateway-faq.yml#what-types-of-logs-does-application-gateway-provide)

**Ansvar**: Kund

**Azure Security Center övervakning**: [Azures säkerhets benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) är standard policy initiativ för Security Center och är grunden för [Security Center rekommendationer](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy-definitioner som är relaterade till den här kontrollen aktive ras automatiskt av Security Center. Aviseringar som är relaterade till den här kontrollen kan kräva en [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan för de relaterade tjänsterna.

**Azure policy inbyggda definitioner – Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: Distribuera Azure Web Application FIREWALL (WAF) framför viktiga webb program för ytterligare inspektion av inkommande trafik. Brand vägg för webbaserade program (WAF) är en tjänst (funktion i Azure Application Gateway) som tillhandahåller centraliserat skydd av dina webb program mot vanliga sårbarheter och sårbarheter. Azure WAF hjälper dig att skydda dina Azure App Service-webbappar genom att inspektera inkommande webb trafik för att blockera attacker som SQL-injektering, skript körning över flera webbplatser, överföringar av skadlig kod och DDoS-attacker. WAF baseras på regler från OWASP (Open Web Application Security Project) Core Rule Set 3,1 (endast WAF_v2), 3,0 och 2.2.9. 

- [Förstå Azure Application Gateway-funktioner](features.md)

- [Förstå Azure-WAF](../web-application-firewall/ag/ag-overview.md)

- [Så här distribuerar du Azure-WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Aktivera DDoS standard skydd på dina virtuella Azure-nätverk som är kopplade till dina produktions instanser av Azure Application Gateway för att skydda mot DDoS-attacker. Använd Azure Security Center integrerad Hot information för att neka kommunikation med kända skadliga IP-adresser.

- [Så här konfigurerar du DDoS-skydd](../ddos-protection/manage-ddos-protection.md)

- [Förstå Azure Security Center integrerad Hot information](../security-center/azure-defender.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: [Azures säkerhets benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) är standard policy initiativ för Security Center och är grunden för [Security Center rekommendationer](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy-definitioner som är relaterade till den här kontrollen aktive ras automatiskt av Security Center. Aviseringar som är relaterade till den här kontrollen kan kräva en [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan för de relaterade tjänsterna.

**Azure policy inbyggda definitioner – Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1,5: registrera nätverks paket

**Vägledning**: för de nätverks säkerhets grupper (NSG: er) som är associerade med dina Azure Application Gateway-undernät aktiverar du NSG Flow-loggar och skickar loggar till ett lagrings konto för trafik granskning. Du kan också skicka NSG Flow-loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflöde i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

Obs! det finns vissa fall där NSG Flow-loggar som är kopplade till dina Azure Application Gateway-undernät inte visar trafik som har tillåtits. Om konfigurationen matchar följande scenario visas inte tillåten trafik i dina NSG-flödes loggar:

- Du har distribuerat Application Gateway v2
- Du har en NSG på Application Gateway-undernätet
- Du har aktiverat NSG Flow-loggar på den NSG

Mer information finns i referenserna nedan.

- [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

- [Förstå nätverks säkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Vanliga frågor och svar om diagnostik och loggning för Azure Application Gateway](./application-gateway-faq.yml#what-types-of-logs-does-application-gateway-provide)

**Ansvar**: Kund

**Azure Security Center övervakning**: [Azures säkerhets benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) är standard policy initiativ för Security Center och är grunden för [Security Center rekommendationer](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy-definitioner som är relaterade till den här kontrollen aktive ras automatiskt av Security Center. Aviseringar som är relaterade till den här kontrollen kan kräva en [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan för de relaterade tjänsterna.

**Azure policy inbyggda definitioner – Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera nätverksbaserade intrångs identifiering/system för skydd mot intrång (ID/IP-adresser)

**Vägledning**: Distribuera Azure Web Application FIREWALL (WAF) framför viktiga webb program för ytterligare inspektion av inkommande trafik. Brand vägg för webbaserade program (WAF) är en tjänst (funktion i Azure Application Gateway) som tillhandahåller centraliserat skydd av dina webb program mot vanliga sårbarheter och sårbarheter. Azure WAF hjälper dig att skydda dina Azure App Service-webbappar genom att inspektera inkommande webb trafik för att blockera attacker som SQL-injektering, skript körning över flera webbplatser, överföringar av skadlig kod och DDoS-attacker. WAF baseras på regler från OWASP (Open Web Application Security Project) Core Rule Set 3,1 (endast WAF_v2), 3,0 och 2.2.9. 

Alternativt finns det flera Marketplace-alternativ som Barracuda-WAF för Azure som är tillgängliga på Azure Marketplace som omfattar funktioner för ID/IP-adresser.

- [Förstå Azure Application Gateway-funktioner](features.md)

- [Förstå Azure-WAF](../web-application-firewall/ag/ag-overview.md)

- [Så här distribuerar du Azure-WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Förstå moln tjänsten Barracuda WAF](../app-service/environment/app-service-app-service-environment-web-application-firewall.md#configuring-your-barracuda-waf-cloud-service)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: Distribuera Azure Application Gateway för webb program med HTTPS/SSL aktiverat för betrodda certifikat.

- [Så här distribuerar du Application Gateway](quick-create-portal.md)

- [Så här konfigurerar du Application Gateway att använda HTTPS](create-ssl-portal.md)

- [Förstå belastnings utjämning för Layer 7 med Azure Web Application Gateway](overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: Använd Virtual Network Service-taggar för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper eller Azure-brandvägg. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet (t. ex. GatewayManager) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

För nätverks säkerhets grupper (NSG: er) som är kopplade till dina Azure Application Gateway-undernät måste du tillåta inkommande Internet trafik på TCP-portarna 65503-65534 för Application Gateway v1 SKU, och TCP-portarna 65200-65535 för v2-SKU: n med mål under nätet som valfri och källa som GatewayManager service tag. Det här port intervallet krävs för kommunikation mellan Azure-infrastrukturen. Dessa portar är skyddade (låsta) av Azure-certifikat. Externa entiteter, inklusive kunder av dessa gateways, kan inte kommunicera med dessa slut punkter.

- [Förstå och använda service märken](../virtual-network/service-tags-overview.md)

- [Översikt över Azure Application Gateway-konfiguration](configuration-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för nätverks inställningar som är relaterade till dina Azure Application Gateway-distributioner. Använd Azure Policy alias i namn området "Microsoft. Network" om du vill skapa anpassade principer för granskning eller framtvinga nätverks konfigurationen för dina Azure Application gateways, virtuella Azure-nätverk och nätverks säkerhets grupper. Du kan också använda inbyggd princip definition.

Du kan också använda Azure-ritningar för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljö artefakter, till exempel Azure Resource Manager mallar, rollbaserad åtkomst kontroll i Azure (Azure RBAC) och principer i en enda skiss definition. Du kan enkelt använda skissen för nya prenumerationer, miljöer och finjustera kontroll och hantering genom versions hantering.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du en Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för nätverks säkerhets grupper (NSG: er) som är kopplade till ditt Azure Application Gateway-undernät samt andra resurser som rör nätverks säkerhets-och trafikflödet. För enskilda NSG-regler använder du fältet Beskrivning för att ange affärs behov och/eller varaktighet (osv.) för alla regler som tillåter trafik till/från ett nätverk.

Använd någon av de inbyggda definitioner av Azure-principer som är relaterade till taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och meddela dig om befintliga otaggade resurser.

Du kan använda Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

- [Så här skapar du en Virtual Network](../virtual-network/quick-create-portal.md)

- [Så här skapar du en NSG med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar för nätverks inställningar och resurser som är relaterade till dina Azure Application Gateway-distributioner. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks inställningar eller resurser sker.

- [Visa och hämta Azure aktivitets logg händelser](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets benchmark för Azure: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: för gransknings loggning i kontroll plan aktiverar du Azure aktivitets logg diagnostikinställningar och skickar loggarna till en Log Analytics-arbetsyta, Azure Event Hub eller Azure Storage-konto. Med hjälp av Azures aktivitets logg data kan du fastställa "vad, vem och när" för alla Skriv åtgärder (skicka, skicka och ta bort) som utförs på kontroll Plans nivån för din Azure Application Gateway och relaterade resurser, till exempel nätverks säkerhets grupper (NSG: er) som används för att skydda Azure Application Gateway-undernätet.

Förutom aktivitets loggar kan du konfigurera diagnostikinställningar för dina Azure Application Gateway-distributioner. diagnostikinställningar används för att konfigurera strömnings export av plattforms loggar och mät värden för en resurs till valfri plats (lagrings konton, Event Hubs och Log Analytics).

Azure Application Gateway erbjuder även inbyggd integrering med Azure Application insikter. Application Insights samlar in logg-, prestanda-och fel data. Application Insights identifierar automatiskt prestanda avvikelser och innehåller kraftfulla analys verktyg som hjälper dig att diagnostisera problem och förstå hur dina webb program används. Du kan aktivera kontinuerlig export för att exportera telemetri från Application Insights till en central plats för att bevara data längre än standard kvarhållningsperioden.

- [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](../azure-monitor/essentials/activity-log.md)

- [Så här aktiverar du diagnostikinställningar för Azure Application Gateway](application-gateway-diagnostics.md)

- [Så här aktiverar du Application Insights](../azure-monitor/app/app-insights-overview.md)

- [Så här konfigurerar du kontinuerlig export](../azure-monitor/app/export-telemetry.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: för gransknings loggning i kontroll plan aktiverar du Azure aktivitets logg diagnostikinställningar och skickar loggarna till en Log Analytics-arbetsyta, Azure Event Hub eller Azure Storage-konto. Med hjälp av Azures aktivitets logg data kan du fastställa "vad, vem och när" för alla Skriv åtgärder (skicka, skicka och ta bort) som utförs på kontroll Plans nivån för din Azure Application Gateway och relaterade resurser, till exempel nätverks säkerhets grupper (NSG: er) som används för att skydda Azure Application Gateway-undernätet.

Förutom aktivitets loggar kan du konfigurera diagnostikinställningar för dina Azure Application Gateway-distributioner. diagnostikinställningar används för att konfigurera strömnings export av plattforms loggar och mät värden för en resurs till valfri plats (lagrings konton, Event Hubs och Log Analytics).

Azure Application Gateway erbjuder även inbyggd integrering med Azure Application insikter. Application Insights samlar in logg-, prestanda-och fel data. Application Insights identifierar automatiskt prestanda avvikelser och innehåller kraftfulla analys verktyg som hjälper dig att diagnostisera problem och förstå hur dina webb program används. Du kan aktivera kontinuerlig export för att exportera telemetri från Application Insights till en central plats för att bevara data längre än standard kvarhållningsperioden.

- [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](../azure-monitor/essentials/activity-log.md)

- [Så här aktiverar du diagnostikinställningar för Azure Application Gateway](application-gateway-diagnostics.md)

- [Så här aktiverar du Application Insights](../azure-monitor/app/app-insights-overview.md)

- [Så här konfigurerar du kontinuerlig export](../azure-monitor/app/export-telemetry.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: i Azure Monitor ställer du in Log Analytics arbets ytans kvarhållningsperiod enligt organisationens regler för efterlevnad. Använd Azure Storage konton för långsiktig/Arkiv lagring.

- [Ange logg lagrings parametrar för Log Analytics arbets ytor](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: Aktivera Azure Activity Log-diagnostikinställningar samt diagnostikinställningar för Azure Application Gateway och skicka loggarna till en Log Analytics arbets yta. Utför frågor i Log Analytics för att söka efter termer, identifiera trender, analysera mönster och tillhandahålla många andra insikter baserat på insamlade data.

Använd Azure Monitor för nätverk för en omfattande vy av hälsa och mått för alla distribuerade nätverks resurser, inklusive Azure Application gatewayer. 

Alternativt kan du aktivera och fordonsbaserad information till Azure Sentinel eller en SIEM från tredje part. 

- [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](../azure-monitor/essentials/activity-log.md)

- [Så här aktiverar du diagnostikinställningar för Azure Application Gateway](application-gateway-diagnostics.md)

- [Använda Azure Monitor för nätverk](../azure-monitor/insights/network-insights-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: Distribuera Azure Web Application FIREWALL (WAF) v2 SKU framför viktiga webb program för ytterligare inspektion av inkommande trafik. Brand vägg för webbaserade program (WAF) är en tjänst (funktion i Azure Application Gateway) som tillhandahåller centraliserat skydd av dina webb program mot vanliga sårbarheter och sårbarheter. Azure WAF hjälper dig att skydda dina Azure App Service-webbappar genom att inspektera inkommande webb trafik för att blockera attacker som SQL-injektering, skript körning över flera webbplatser, överföringar av skadlig kod och DDoS-attacker. WAF baseras på regler från OWASP (Open Web Application Security Project) Core Rule Set 3,1 (endast WAF_v2), 3,0 och 2.2.9. 

Aktivera diagnostikinställningar för Azure aktivitets logg samt diagnostikinställningar för Azure-WAF och skicka loggarna till en Log Analytics-arbetsyta. Utför frågor i Log Analytics för att söka efter termer, identifiera trender, analysera mönster och tillhandahålla många andra insikter baserat på insamlade data.  Du kan skapa aviseringar baserat på Log Analytics arbets ytans frågor.

Använd Azure Monitor för nätverk för en omfattande vy av hälsa och mått för alla distribuerade nätverks resurser, inklusive Azure Application gatewayer. I konsolen Azure Monitor för nätverk kan du Visa och skapa aviseringar för Azure Application Gateway.

- [Så här distribuerar du Azure-WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](../azure-monitor/essentials/activity-log.md)

- [Så här aktiverar du diagnostikinställningar för Azure Application Gateway](application-gateway-diagnostics.md)

- [Använda Azure Monitor för nätverk](../azure-monitor/insights/network-insights-overview.md)

- [Så här skapar du aviseringar i Azure](../azure-monitor/alerts/tutorial-response.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: Distribuera Azure Web Application FIREWALL (WAF) v2 framför viktiga webb program för ytterligare inspektion av inkommande trafik. Brand vägg för webbaserade program (WAF) är en tjänst (funktion i Azure Application Gateway) som tillhandahåller centraliserat skydd av dina webb program mot vanliga sårbarheter och sårbarheter. Azure WAF hjälper dig att skydda dina Azure App Service-webbappar genom att inspektera inkommande webb trafik för att blockera attacker som SQL-injektering, skript körning över flera webbplatser, överföringar av skadlig kod och DDoS-attacker.

Konfigurera diagnostikinställningar för dina Azure Application Gateway-distributioner. diagnostikinställningar används för att konfigurera strömnings export av plattforms loggar och mät värden för en resurs till valfri plats (lagrings konton, Event Hubs och Log Analytics).

- [Så här distribuerar du Azure-WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Konfigurera diagnostikinställningar för Azure-WAF](application-gateway-diagnostics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security benchmark: identitet och Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: Azure Active Directory (Azure AD) har inbyggda roller som måste tilldelas explicit och som kan frågas. Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

- [Så här hämtar du en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: kontroll Plans åtkomst till Azure Application Gateway styrs via Azure Active Directory (Azure AD). Azure AD har inte begreppet standard lösen ord.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton. Använd Azure Security Center identitets-och åtkomst hantering för att övervaka antalet administrativa konton.

För att hjälpa dig att hålla koll på dedikerade administrativa konton kan du dessutom använda rekommendationer från Azure Security Center eller inbyggda Azure-principer, t. ex.:

- Det bör finnas fler än en ägare som tilldelats din prenumeration
- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med ägar behörigheter bör tas bort från din prenumeration

Mer information finns i referenserna nedan.

- [Använda Azure Security Center för att övervaka identitet och åtkomst (för hands version)](../security-center/security-center-identity-access.md)

- [Använda Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Använd Azure Active Directory enkel inloggning (SSO)

**Vägledning**: Använd en Azure App-registrering (tjänstens huvud namn) för att hämta en token som kan användas för att interagera med dina Azure Application-gatewayer via API-anrop.

- [Så här anropar du Azure REST API: er](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Registrera klient programmet (tjänstens huvud namn) med Azure Active Directory (Azure AD)](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Information om Azure Recovery Services API](/rest/api/recoveryservices/)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory-baserad åtkomst

**Vägledning**: Aktivera Azure Active Directory (Azure AD) multifaktorautentisering och Azure Security Center Följ rekommendationerna för identitets-och åtkomst hantering.

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Använd säkra, Azure-hanterade arbets stationer för administrativa uppgifter

**Vägledning**: Använd Paw (Privileged Access Workstation) med multifaktorautentisering konfigurerad för att logga in på och konfigurera Azure-resurser.

- [Lär dig mer om arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: använda Azure Active Directory (Azure AD) säkerhets rapporter för generering av loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure Security Center för att övervaka identitets-och åtkomst aktiviteter.

- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här övervakar du användarnas identitets- och åtkomstrelaterade aktiviteter i Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Använd villkorlig åtkomst med namngivna platser för att tillåta åtkomst från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner.

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: använda Azure Active Directory (Azure AD) som central autentiserings-och auktoriserings system. Azure AD skyddar data med stark kryptering för data i vila och under överföring. Azure AD innehåller även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

- [Skapa och konfigurera en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure Active Directory (Azure AD) innehåller loggar som hjälper till att identifiera inaktuella konton. Dessutom kan du använda Azure Identity Access-granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användar åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

- [Förstå Azure AD repor ting](../active-directory/reports-monitoring/index.yml)

- [Så här använder du granskningar av Azure Identity Access](../active-directory/governance/access-reviews-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: du har åtkomst till Azure Active Directory (Azure AD) inloggnings aktivitet, gransknings-och risk händelse logg källor, som gör att du kan integrera med alla Siem/övervaknings verktyg.

Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka gransknings loggar och inloggnings loggar till en Log Analytics-arbetsyta. Du kan konfigurera önskade aviseringar i Log Analytics arbets ytan.

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: varning vid inloggnings beteende för konto

**Vägledning**: Använd Azure Active Directory (Azure AD) identitets skydd och funktioner för identifiering av risker för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter. Du kan också mata in data i Azure Sentinel för ytterligare undersökning.

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information. 

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera separata prenumerationer och/eller hanterings grupper för utveckling, testning och produktion. Kontrol lera att alla Virtual Network Azure Application Gateway-undernätet har en nätverks säkerhets grupp (NSG) som tillämpas med nätverks åtkomst kontroller som är specifika för programmets betrodda portar och källor. Nätverks säkerhets grupper stöds på Azure Application Gateway, men det finns vissa begränsningar och krav som måste följas för att din NSG och Azure Application-Gateway ska fungera som förväntat.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du hanterings grupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

- [Förstå begränsningar och krav för att använda NSG: er med Azure Application Gateway](configuration-overview.md)

- [Så här skapar du en NSG med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: kontrol lera att alla Virtual Network Azure Application Gateway-undernätet har en nätverks säkerhets grupp (NSG) som tillämpas med nätverks åtkomst kontroller som är specifika för programmets betrodda portar och källor. Begränsa utgående trafik till endast betrodda platser för att minska risken för data exfiltrering. Nätverks säkerhets grupper stöds på Azure Application Gateway, men det finns vissa begränsningar och krav som måste följas för att din NSG och Azure Application-Gateway ska fungera som förväntat.

- [Förstå begränsningar och krav för att använda NSG: er med Azure Application Gateway](configuration-overview.md)

- [Så här skapar du en NSG med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: Konfigurera kryptering från slut punkt till slut punkt med TLS för dina Azure Application-gatewayer.

- [Så här konfigurerar du end-to-end-TLS med hjälp av Azure Application Gateway](end-to-end-ssl-portal.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: Använd rollbaserad åtkomst kontroll för att kontrol lera åtkomst till resurser

**Vägledning**: Använd rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att styra åtkomsten till Azure Application Gateway-kontroll planet (Azure Portal).

- [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i produktion Azure Application Gateway-instanser samt andra kritiska eller relaterade resurser.

- [Så här skapar du aviseringar för Azure aktivitets logg händelser](../azure-monitor/alerts/alerts-activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security benchmark: sårbarhet Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: inte tillgänglig för tillfället. sårbarhets bedömning i Azure Security Center är ännu inte tillgängligt för Azure Application Gateway.

Underliggande plattform som genomsöks och korrigeras av Microsoft. Granska säkerhets kontroller som är tillgängliga för Azure Application Gateway för att minska konfigurations problem.

- [Funktions täckning (inklusive sårbarhets bedömning) för Azure PaaS Services](../security-center/features-paas.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: ännu inte tillgänglig; sårbarhets bedömning i Azure Security Center är ännu inte tillgängligt för Azure Application Gateway.

Underliggande plattform som genomsöks och korrigeras av Microsoft. Granska säkerhets kontroller som är tillgängliga för Azure Application Gateway för att minska konfigurations problem.

- [Funktions täckning (inklusive sårbarhets bedömning) för Azure PaaS Services](../security-center/features-paas.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: ännu inte tillgänglig; sårbarhets bedömning i Azure Security Center är ännu inte tillgängligt för Azure Application Gateway.

Underliggande plattform som genomsöks och korrigeras av Microsoft. Granska säkerhets kontroller som är tillgängliga för Azure Application Gateway för att minska konfigurations problem.

- [Funktions täckning (inklusive sårbarhets bedömning) för Azure PaaS Services](../security-center/features-paas.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga/identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk, portar och protokoll osv.) i din prenumeration (er). Se till att du har rätt (Läs) behörigheter i din klient organisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via resurs diagram, rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser som går framåt.

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser som ger metadata till att logiskt organisera dem i en taxonomi.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer om det behövs för att organisera och spåra Azure-resurser. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

Använd dessutom Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper
- Tillåtna resurstyper

Mer information finns i referenserna nedan.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du hanterings grupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i dina prenumerationer. 

Använd Azure Resource Graph för att fråga/identifiera resurser i sina prenumerationer.  Se till att alla Azure-resurser som finns i miljön är godkända. 

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

Mer information finns i referenserna nedan.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Konfigurera villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: implementera separata prenumerationer och/eller hanterings grupper för utveckling, testning och produktion. Kontrol lera att alla Virtual Network Azure Application Gateway-undernätet har en nätverks säkerhets grupp (NSG) som tillämpas med nätverks åtkomst kontroller som är specifika för programmets betrodda portar och källor. Nätverks säkerhets grupper stöds på Azure Application Gateway, men det finns vissa begränsningar och krav som måste följas för att din NSG och Azure Application-Gateway ska fungera som förväntat.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du hanterings grupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

- [Förstå begränsningar och krav för att använda NSG: er med Azure Application Gateway](configuration-overview.md)

- [Så här skapar du en NSG med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security benchmark: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för nätverks inställningar som är relaterade till dina Azure Application Gateway-distributioner. Använd Azure Policy alias i namn området "Microsoft. Network" om du vill skapa anpassade principer för granskning eller framtvinga nätverks konfigurationen för dina Azure Application gateways, virtuella Azure-nätverk och nätverks säkerhets grupper. Du kan också använda inbyggd princip definition.

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [Deny] och [distribuera om det inte finns] för att genomdriva säkra inställningar i dina Azure-resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: om du använder anpassade definitioner av Azure-principer använder du Azure DevOps eller Azure databaser för att lagra och hantera din kod på ett säkert sätt.

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Dokumentation om Azure databaser](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: använd inbyggda Azure policy definitioner samt Azure policy alias i namn området "Microsoft. Network" för att skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Dessutom kan du utveckla en process och pipeline för att hantera princip undantag.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: använd inbyggda Azure policy definitioner samt Azure policy alias i namn området "Microsoft. Network" för att skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Använd Azure-princip [granskning], [neka] och [distribuera om det inte finns] för att automatiskt tillämpa konfigurationer för dina Azure-resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Använd hanterade identiteter för att tillhandahålla Azure Application Gateway med en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Med hanterade identiteter kan du autentisera till vilken tjänst som helst som stöder Azure AD-autentisering, inklusive Key Vault utan autentiseringsuppgifter i din kod.

Använd Azure Key Vault för att lagra certifikat på ett säkert sätt. Azure Key Vault är ett plattforms hanterat hemligt arkiv som du kan använda för att skydda hemligheter, nycklar och SSL-certifikat. Azure Application Gateway stöder integrering med Key Vault för Server certifikat som är anslutna till HTTPS-aktiverade lyssnare. Detta stöd är begränsat till Application Gateway v2-SKU: n.

- [Så här konfigurerar du SSL-avslutning med Key Vault certifikat med hjälp av Azure PowerShell](configure-keyvault-ps.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: Använd hanterade identiteter för att tillhandahålla Azure Application Gateway med en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Med hanterade identiteter kan du autentisera till vilken tjänst som helst som stöder Azure AD-autentisering, inklusive Key Vault utan autentiseringsuppgifter i din kod.

Använd Azure Key Vault för att lagra certifikat på ett säkert sätt. Azure Key Vault är ett plattforms hanterat hemligt arkiv som du kan använda för att skydda hemligheter, nycklar och SSL-certifikat. Azure Application Gateway stöder integrering med Key Vault för Server certifikat som är anslutna till HTTPS-aktiverade lyssnare. Detta stöd är begränsat till Application Gateway v2-SKU: n.

- [Så här konfigurerar du SSL-avslutning med Key Vault certifikat med hjälp av Azure PowerShell](configure-keyvault-ps.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault. 

- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security benchmark: skydd mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Använd en centralt hanterad program vara mot skadlig kod

**Vägledning**: Distribuera Azure Web Application FIREWALL (WAF) v2 framför viktiga webb program för ytterligare inspektion av inkommande trafik. Brand vägg för webbaserade program (WAF) är en tjänst (funktion i Azure Application Gateway) som tillhandahåller centraliserat skydd av dina webb program mot vanliga sårbarheter och sårbarheter. Azure WAF hjälper dig att skydda dina Azure App Service-webbappar genom att inspektera inkommande webb trafik för att blockera attacker som SQL-injektering, skript körning över flera webbplatser, överföringar av skadlig kod och DDoS-attacker.

Konfigurera diagnostikinställningar för dina Azure Application Gateway-distributioner. diagnostikinställningar används för att konfigurera strömnings export av plattforms loggar och mät värden för en resurs till valfri plats (lagrings konton, Event Hubs och Log Analytics).

- [Så här distribuerar du Azure-WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Konfigurera diagnostikinställningar för Azure-WAF](application-gateway-diagnostics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för skadlig program vara uppdateras

**Vägledning**: när du använder Azure WebApplication-BRANDVÄGGEN (WAF) kan du konfigurera WAF-principer. En WAF-princip består av två typer av säkerhets regler: anpassade regler som har skapats av kunden och hanterade regel uppsättningar som är en samling av Azure-hanterade förkonfigurerade uppsättningar med regler. Azure-hanterade regel uppsättningar ger ett enkelt sätt att distribuera skydd mot en gemensam uppsättning säkerhetshot. Eftersom sådana rulesets hanteras av Azure uppdateras reglerna vid behov för att skydda mot nya attack-signaturer.

- [Förstå Azure-hanterade WAF regel uppsättningar](../web-application-firewall/ag/ag-overview.md#waf-policy-and-rules)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Azure Security benchmark: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Säkerställ regelbunden automatisk säkerhets kopiering

**Vägledning**: Azure Application Gateway lagrar inte kund information. Men om du använder anpassade Azure policy-definitioner, använder du Azure DevOps eller Azure databaser för att lagra och hantera din kod på ett säkert sätt.

Azure DevOps Services utnyttjar många av funktionerna i Azure Storage för att säkerställa att data är tillgängliga i händelse av maskinvaruproblem, avbrott i tjänsten eller haveri. Azure DevOps-teamet följer dessutom procedurer för att skydda data från oavsiktlig eller skadlig borttagning.

- [Förstå data tillgänglighet i Azure DevOps](/azure/devops/organizations/security/data-protection?preserve-view=true&view=azure-devops#data-availability)

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Dokumentation om Azure databaser](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: säkerhetskopiera Kundhanterade certifikat inom Azure Key Vault.

- [Säkerhetskopiera Key Vault-certifikat i Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: testa återställning av säkerhetskopierade kund hanterade certifikat.

- [Återställa Key Vault-certifikat](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: se till att mjuk borttagning är aktiverat för Azure Key Vault. Med mjuk borttagning kan du återställa borttagna nyckel valv och valv objekt, till exempel nycklar, hemligheter och certifikat.

- [Använda Azure Key Vault ' mjuk borttagning '](../key-vault/general/key-vault-recovery.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.

- [Konfigurera automatisering av arbets flöden i Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Kunden kan också utnyttja NISTs hanterings guide för dator säkerhet för att hjälpa till med att skapa egna incident svars planer](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller analysera den som används för att utfärda aviseringen samt den konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Dessutom är det tydligt att markera prenumerationer (t. ex. produktion, icke-Prod) och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems incident svars funktioner på en vanlig takt. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [Se NIST: guide för test, utbildning och övnings program för IT-planer och funktioner](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har öppnats av en olaglig eller obehörig part.  Granska incidenter när du är säker på att problemen är lösta.

- [Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöden i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer.

- [Konfigurera automatisering av arbets flöden och Logic Apps](../security-center/workflow-automation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [övningen för Azure Security benchmark: inträngande tester och röda team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: Följ Microsoft Cloud inträngande test för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft. 

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)
