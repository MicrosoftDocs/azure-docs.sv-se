---
title: Azure-säkerhetsbaslinje för Azure Load Balancer
description: Den Azure Load Balancer säkerhetsbaslinjen ger procedurvägledning och resurser för implementering av säkerhetsrekommendationerna som anges i Azure Security Benchmark.
author: msmbaldwin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 64e0a8cfcaf00c55038fbe5d1cdc7423b681b690
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589269"
---
# <a name="azure-security-baseline-for-azure-load-balancer"></a>Azure-säkerhetsbaslinje för Azure Load Balancer

Den här säkerhetsbaslinjen tillämpar vägledning [från Azure Security Benchmark version 1.0](../security/benchmarks/overview-v1.md) för Microsoft Azure Load Balancer. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure.
Innehållet grupperas av de säkerhetskontroller **som definieras** av Azure Security Benchmark och de relaterade riktlinjer som gäller för Azure Load Balancer. **Kontroller** som inte är Azure Load Balancer har undantagits.

 
Information om hur Azure Load Balancer mappar helt till Azure Security Benchmark finns i den fullständiga [mappningsfilen Azure Load Balancer säkerhetsbaslinje.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Skydda Azure-resurser i virtuella nätverk

**Vägledning:** Använd interna Azure Load Balancers för att endast tillåta trafik till serverresurser inifrån vissa virtuella nätverk eller peer-ade virtuella nätverk utan att exponeras för Internet. Implementera en extern Load Balancer med källnätverk

Address Translation (SNAT) för att maskera IP-adresserna för backend-resurser för skydd mot direkt Internetexponering.

Azure erbjuder två typer av Load Balancer, Standard och Basic. Använd Standard Load Balancer för alla produktionsarbetsbelastningar. Implementera nätverkssäkerhetsgrupper och tillåt endast åtkomst till programmets betrodda portar och IP-adressintervall. I de fall där det inte finns någon nätverkssäkerhetsgrupp tilldelad till serverundernätet eller nätverkskortet för de virtuella serverdatorerna, tillåts inte trafik till dessa resurser från lastbalanseraren. Med Standard Load Balancers anger du utgående regler för att definiera utgående NAT med en nätverkssäkerhetsgrupp. Granska dessa regler för utgående trafik för att justera beteendet för dina utgående anslutningar.

Att använda en Standard Load Balancer rekommenderas för dina produktionsarbetsbelastningar och vanligtvis används Basic Load Balancer endast för testning eftersom den grundläggande typen är öppen för anslutningar från Internet som standard och inte kräver nätverkssäkerhetsgrupper för drift.

- [Utgående anslutningar i Azure](load-balancer-outbound-connections.md)

- [Uppgradera offentliga Azure-Load Balancer](upgrade-basic-standard.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Övervaka och logga konfiguration och trafik för virtuella nätverk, undernät och nätverkskort

**Vägledning:** Load Balancer är en direkttjänst eftersom den förlitar sig på reglerna för nätverkssäkerhetsgrupper som tillämpas på backend-resurser och de konfigurerade reglerna för utgående trafik för att kontrollera Internetåtkomst.

Granska de regler för utgående trafik som konfigurerats för din Standard Load Balancer via bladet Regler för utgående trafik i din Load Balancer och bladet regler för belastningsutjämning där du kan ha implicita regler för utgående trafik aktiverade.

Övervaka antalet utgående anslutningar för att spåra hur ofta dina resurser når internet. 

Använd Security Center och följ nätverksskyddsrekommendationerna för att skydda dina Azure-nätverksresurser.

Följ säkerhetsrekommendationerna för dina backend-resurser och aktivera flödesloggar för nätverkssäkerhetsgruppen och skicka loggarna till ett Azure Storage-konto för granskning.

Skicka även flödesloggarna till en Log Analytics-arbetsyta och använd Trafikanalys för att ge insikter om trafikmönster i ditt Azure-moln. Fördelarna med Trafikanalys bland annat möjligheten att visualisera nätverksaktivitet, identifiera hotpunkter och säkerhetshot, förstå trafikflödesmönster och hitta felkonfigurationer i nätverk.

- [Så här aktiverar du flödesloggar för nätverkssäkerhetsgrupp](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

- [Förstå nätverkssäkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Hur gör jag för att min statistik för utgående anslutningar](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-outbound-connection-statistics)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1.3: Skydda kritiska webbprogram

**Vägledning:** Definiera uttryckligen Internetanslutning och giltiga käll-IP-adresser via utgående regler och nätverkssäkerhetsgrupper med din Load Balancer att använda Microsofts hotinformation för att skydda dina webbprogram.

- [Integrera Azure Firewall](../firewall/integrate-lb.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

**Vägledning:** Aktivera Standardskydd för Azure Distributed Denial of Service (DDoS) på azure-Virtual Network för att skydda mot DDoS-attacker. 

Distribuera Azure Firewall vid var och en av organisationens nätverksgränser med hotinformationsbaserad filtrering aktiverad och konfigurerad för att "varna och neka" för skadlig nätverkstrafik.

 

Använd Security Center skydd mot hot för att identifiera kommunikation med kända skadliga IP-adresser. 

Den Standard Load Balancer är utformad för att vara säker som standard och en del av en privat och isolerad Virtual Network. Den är stängd för inkommande flöden om den inte öppnas av nätverkssäkerhetsgrupper för att uttryckligen tillåta tillåten trafik och för att förhindra kända skadliga IP-adresser. Om det inte finns en nätverkssäkerhetsgrupp i ett undernät eller nätverkskort för den virtuella datorresursen bakom Load Balancer kan inte trafik nå den här resursen. 

Distribuera Azure Firewall vid var och en av organisationens nätverksgränser med filtrering baserad på hotinformation aktiverad och konfigurerad för att "varna och neka" för skadlig nätverkstrafik för att förhindra attacker från skadliga IP-adresser. 

Implementera vägledning för att integrera Azure Firewall med dina Load Balancer.

Använd Security Center för att identifiera kommunikation med kända skadliga IP-adresser. 

Security Center (standardnivå) ger just-in-time-åtkomst till virtuella datorer och konfigurerar tillåtna käll-IP-adresser för att endast tillåta åtkomst från godkända IP-adresser/-intervall.
 

Använd Security Center anpassningsbar nätverkshärdning för att rekommendera konfigurationer av nätverkssäkerhetsgrupp som begränsar portar och käll-IP-adresser baserat på faktisk trafik och hotinformation.
 

- [Hantera Azure DDoS Protection Standard med hjälp av Azure Portal](../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall filtrering baserad på hotinformation](../firewall/threat-intel.md)

- [Skydd mot hot i Azure Security Center](../security-center/azure-defender.md)

- [Skydda dina hanteringsportar med just-in-time-åtkomst](../security-center/security-center-just-in-time.md)

- [Anpassningsbar nätverkshärdning i Azure Security Center](../security-center/security-center-adaptive-network-hardening.md)

- [Integrera Azure Firewall med din Load Balancer](../firewall/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1.5: Registrera nätverkspaket

**Vägledning:** Aktivera Network Watcher paketinfångst för att undersöka avvikande aktiviteter.

- [Så här skapar du en Network Watcher instans](../network-watcher/network-watcher-create.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuera nätverksbaserad intrångsidentifiering/intrångsskyddssystem (IDS/IPS)

**Vägledning:** Implementera ett erbjudande från Azure Marketplace som stöder IDS/IPS-funktioner med funktioner för nyttolastgranskning i miljön för din Load Balancer. 

Använd Azure Firewall hotinformation om kontroll av nyttolaster inte är ett krav. Azure Firewall filtrering baserad på hotinformation används för att varna om och/eller blockera trafik till och från kända skadliga IP-adresser och domäner. IP-adresserna och domänerna hämtas från Microsoft Threat Intelligence-flödet.

Distribuera valfri brandväggslösning vid var och en av organisationens nätverksgränser för att identifiera och/eller blockera skadlig trafik.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Så här distribuerar du Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Så här konfigurerar du aviseringar med Azure Firewall](../firewall/threat-intel.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Hantera trafik till webbprogram

**Vägledning:** Definiera uttryckligen Internetanslutning och giltiga käll-IP-adresser via utgående regler och nätverkssäkerhetsgrupper med din Load Balancer för att använda Microsofts hotinformationsfunktioner för att skydda dina webbprogram.

- [Integrera Azure Firewall](../firewall/integrate-lb.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexiteten och det administrativa arbetet med nätverkssäkerhetsregler

**Vägledning:** Använd tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Ange tjänsttaggnamnet i käll- eller målfältet för en regel för att tillåta eller neka trafik för motsvarande tjänst. 

Microsoft hanterar de adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras. 

Som standard innehåller varje nätverkssäkerhetsgrupp tjänsttaggen AzureLoadBalancer för att tillåta hälsoavsökningstrafik. 

Se Azure-dokumentationen för alla tjänsttaggar som är tillgängliga för användning i regler för nätverkssäkerhetsgrupp.

- [Tillgängliga tjänsttaggar](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Underhålla standardsäkerhetskonfigurationer för nätverksenheter

**Vägledning:** Definiera och implementera standardsäkerhetskonfigurationer för nätverksresurser med Azure Policy.

Använd Azure Blueprints för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljöartefakter, till exempel Azure Resources Manager-mallar, Azure RBAC-kontroller och principer, i en enda skissdefinition. 

Tillämpa skissen på nya prenumerationer och finjustera kontroll och hantering via versionshantering.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy exempel för nätverk](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Så här skapar du en Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentera trafikkonfigurationsregler

**Vägledning:** Använda resurstaggar för nätverkssäkerhetsgrupper och andra resurser som rör nätverkssäkerhet och trafikflöde. 

Använd fältet "Beskrivning" för att dokumentera reglerna som tillåter trafik till/från ett nätverk för enskilda regler för nätverkssäkerhetsgruppen.

Implementera någon av de inbyggda Azure Policy-definitioner som rör taggning, till exempel "Kräv tagg och dess värde", vilket säkerställer att alla resurser skapas med taggar och för att meddela om eventuella befintliga otaggade resurser.

Använd Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

- [Så här skapar du en Azure-Virtual Network](../virtual-network/quick-create-portal.md)

- [Så här filtrerar du nätverkstrafik med regler för nätverkssäkerhetsgrupp](../virtual-network/tutorial-filter-network-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

**Vägledning:** Använd Azure-aktivitetsloggen för att övervaka resurskonfigurationer och identifiera ändringar i dina Azure-resurser. 

Skapa aviseringar i Azure Monitor meddela dig när viktiga resurser ändras.

- [Så här visar och hämtar du Händelser i Azure-aktivitetsloggen](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [Prestandatest för Azure-säkerhet: Loggning och övervakning.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera central hantering av säkerhetsloggar

**Vägledning:** Granska ändringar i dina utgående regler och nätverkssäkerhetsgrupper för dina lastbalanserare genom att visa aktivitetsloggen i dina prenumerationer. 

Visa de interna värdloggarna för att se till att dina backend-resurser är säkra.

Exportera loggarna till Log Analytics eller någon annan lagringsplattform. I Azure Monitor använder du Log Analytics-arbetsytor för att köra frågor mot och utföra analyser och använda Azure Storage-konton för långsiktig lagring och arkiveringslagring.

Aktivera och ta med dessa data för att Azure Sentinel siem från tredje part baserat på organisationens affärskrav.

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Samla in plattformsloggar och mått med Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Så här samlar du in interna värdloggar för virtuella Azure-datorer med Azure Monitor](../azure-monitor/vm/quick-collect-azurevm.md)

- [Så här kommer du igång Azure Monitor siem-integrering från tredje part](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Plattformsaktivitetsloggar](../azure-monitor/essentials/activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägledning:** Granska loggnings- och granskningsinformationen för kontroll- och hanteringsplanet som har avbildats med aktivitetsloggar för Grundläggande Load Balancer. Dessa av insamlingsinställningar är aktiverade som standard. 

Använd aktivitetsloggar för att övervaka åtgärder på resurser för att visa all aktivitet och deras status. 

Bestäm vilka åtgärder som har vidtagits på resurserna i din prenumeration med aktivitetsloggar: 

- vem som startade åtgärden
- när åtgärden inträffade
- status för åtgärden

- värdena för andra egenskaper som kan hjälpa dig undersöka åtgärden

Hämta information från aktivitetsloggen via Azure PowerShell, Azure-kommandoradsgränssnittet (CLI), Azure REST API eller Azure Portal. 

Implementera flerdimensionell diagnostik för Standard Load Balancer konfigurationsfunktioner med Azure Monitor.  Dessa omfattar tillgängliga säkerhetsmått som innehåller information om SNAT-anslutningar (Source Network Address Translation), portar. Ytterligare mått för SYN-paket (synkronisera) och paketräknare är också tillgängliga. 

Hämta flerdimensionella mått programmatiskt via API:er och skriv dem till ett lagringskonto via alternativet Alla mått.

Använd Innehållspaketet för Azure-granskningsloggar med Microsoft Power BI för att analysera dina data med förkonfigurerade instrumentpaneler eller för att anpassa vyerna baserat på dina krav.

Strömma loggar till en händelsehubb eller en Log Analytics-arbetsyta. De kan också extraheras från Azure Blob Storage och visas i olika verktyg, till exempel Excel och Power BI. 

Aktivera och publicera data för att Azure Sentinel siem från tredje part baserat på dina affärsbehov.

- [Läs den här artikeln med stegvisa instruktioner för varje metod som beskrivs i Granskningsåtgärder med Resource Manager](../azure-resource-manager/management/view-activity-logs.md)

- [Azure Monitor-loggar för offentlig Basic Load Balancer](load-balancer-monitor-log.md)

- [Visa aktivitetsloggar för att övervaka åtgärder för resurser](../azure-resource-manager/management/view-activity-logs.md)

- [Hämta flerdimensionella mått programmatiskt via API:er](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#retrieve-multi-dimensional-metrics-programmatically-via-apis)

- [Så här kommer du igång Azure Monitor siem-integrering från tredje part](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera kvarhållning av säkerhetslogglagring

**Vägledning:** Aktivitetsloggen är aktiverad som standard och bevaras i 90 dagar i Azures händelseloggarkiv. Ange kvarhållningsperioden för Log Analytics-arbetsytan enligt organisationens efterlevnadsregler i Azure Monitor. Använd Azure Storage-konton för långsiktig lagring och arkiveringslagring.

- [Artikel om att visa aktivitetsloggar för att övervaka åtgärder för resurser](../azure-resource-manager/management/view-activity-logs.md)

- [Ändra kvarhållningsperioden för data i Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Så här konfigurerar du bevarandeprincip för Azure Storage-kontologgar](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning:** Övervaka, hantera och felsöka Standard Load Balancer resurser med hjälp av Load Balancer på sidan Azure Portal och Resource Health under Azure Monitor. Tillgängliga säkerhetsmått är information om SNAT-anslutningar (Source Network Address Translation), portar. Dessutom är mått för SYN-paket (synkronisera) och paketräknare också tillgängliga. 

Använd Azure Monitor för att granska status för slutpunktshälsoavsökning med flerdimensionella mått för Standard, externa och interna lastbalanserare. Samla in dessa mått programmatiskt via API:er och skrivs till ett lagringskonto via alternativet "Alla mått".

Azure Monitor är inte tillgängliga för interna grundläggande lastbalanserare. 

Använd Azure Monitor för att se hälsoavsökningens status sammanfattad per backend-pool för basic extern Load Balancer, till exempel antalet instanser i din backend-pool som inte tar emot begäranden från Load Balancer på grund av fel i hälsoavsökningen. 

Implementera loggning med Azure Operational Insights för att tillhandahålla statistik om lastbalanserarens hälsostatus. 

Använd aktivitetsloggarna för att visa aviseringar och övervaka åtgärder för resurser och deras status i dina Azure-prenumerationer. Aktivitetsloggar är aktiverade som standard och kan visas i Azure Portal. 

Använd Microsoft Power BI med innehållspaketet för Azure-granskningsloggar och analysera dina data med förkonfigurerade instrumentpaneler. Anpassa vyerna så att de passar dina behov baserat på företagets behov. 

Strömma loggar till en händelsehubb eller en Log Analytics-arbetsyta. De kan också extraheras från Azure Blob Storage och visas i olika verktyg, till exempel Excel och Power BI. Du kan aktivera och ta med data Azure Sentinel siem eller siem från tredje part.

- [Hälsoavsökningar i Load Balancer](load-balancer-custom-probe-overview.md)

- [REST-API:et för Azure Monitor](/rest/api/monitor)

- [Så här hämtar du mått via REST API](/rest/api/monitor/metrics/list)

- [Standard Load Balancer diagnostik med mått, aviseringar och resurshälsa](load-balancer-standard-diagnostics.md)

- [Azure Monitor-loggar för offentlig Basic Load Balancer](load-balancer-monitor-log.md)

- [Visa mått för lastbalanserare i Azure Portal](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#view-your-load-balancer-metrics-in-the-azure-portal)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivera aviseringar för avvikande aktiviteter

**Vägledning:** Använd Security Center Log Analytics-arbetsyta för övervakning och avisering om avvikande aktivitet relaterad till Load Balancer i säkerhetsloggar och händelser.

Aktivera och publicera data för att Azure Sentinel siem-verktyg från tredje part.

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Hantera aviseringar i Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Så här varnar du om loggdata i Log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Upprätthålla en förteckning över administrativa konton

**Vägledning:** Med rollbaserad åtkomstkontroll i Azure (Azure RBAC) kan du hantera åtkomsten till Azure-resurser, till exempel Load Balancer via rolltilldelningar. Tilldela dessa roller till användare, grupper av tjänstens huvudnamn och hanterade identiteter.

Inventera fördefinierade och inbyggda roller för vissa resurser med verktyg som Azure CLI, Azure PowerShell eller Azure Portal.

- [Så här får du en katalogroll i Azure Active Directory (Azure AD) med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Så här hämtar du medlemmar i en katalogroll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använd multifaktorautentisering för Azure Active Directory åtkomst

**Vägledning:** Aktivera Azure Active Directory (Azure AD) multifaktorautentisering och följ Security Center rekommendationer för identitets- och åtkomsthantering.

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Övervaka identitet och åtkomst inom Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Använd dedikerade datorer (arbetsstationer för privilegierad åtkomst) för alla administrativa uppgifter

**Vägledning:** Använd PAW (Privileged Access Workstations) med multifaktorautentisering som konfigurerats för att hantera och komma åt Azure-nätverksresurser. 

- [Läs mer om arbetsstationer för privilegierad åtkomst](/security/compass/privileged-access-devices)

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Hantera Azure-resurser endast från godkända platser

**Vägledning:** Använd namngivna platser för villkorsstyrd åtkomst för att endast tillåta åtkomst från specifika logiska gruppningar av IP-adressintervall eller länder/regioner.

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning:** Använd Azure Active Directory (Azure AD) som ett centralt system för autentisering och auktorisering för dina tjänster. Azure AD skyddar data med hjälp av stark kryptering för data i vila och under överföring och även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.  

- [Skapa och konfigurera en Azure AD-instans](../active-directory-domain-services/tutorial-create-instance.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Granska och stämma av användaråtkomst regelbundet

**Vägledning:** Använd Azure Active Directory (Azure AD) för att tillhandahålla loggar som hjälper dig att identifiera inaktuella konton. 

Azure Identity Access Reviews kan utföras för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Användaråtkomsten bör granskas regelbundet för att se till att endast de aktiva användarna har fortsatt åtkomst.

- [Förstå Azure AD-rapportering](/azure/active-directory/reports-monitoring/)

- [Så här använder du Azure Identity Access Reviews](../active-directory/governance/access-reviews-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning:** Integrera Azure Active Directory (Azure AD) inloggningsaktivitet, källor för gransknings- och riskhändelseloggar med valbara SIEM- eller övervakningsverktyg baserat på din åtkomst.

Effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka granskningsloggar och inloggningsloggar till en Log Analytics-arbetsyta. Du kan konfigurera önskade aviseringar i Log Analytics-arbetsytan.

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Avisering om kontoinloggningens beteendeavvikelse

**Vägledning:** Använd Azure Active Directory (Azure AD) funktioner för risk och identitetsskydd för att konfigurera automatiserade svar på identifierade misstänkta åtgärder relaterade till användaridentiteter. Mata in data i Azure Sentinel för ytterligare undersökningar.

- [Så visar du riskfyllda inloggningar för Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Så här konfigurerar och aktiverar du riskprinciper för Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Kryptera all känslig information under överföring

**Vägledning:** Se till att alla klienter som ansluter till dina Azure-resurser kan förhandla TLS 1.2 eller högre.

Följ Azure Security Center rekommendationer för kryptering i vila och kryptering under överföring, om tillämpligt.

- [Förstå kryptering under överföring med Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Använd Azure RBAC för att hantera åtkomst till resurser

**Vägledning:** Använd Azure RBAC för att styra åtkomsten till Load Balancer resurser.

- [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Använd värdbaserad dataförlustskydd för att framtvinga åtkomstkontroll

**Vägledning:** Load Balancer är en direkttjänst som inte lagrar kunddata. Det är en del av den underliggande plattformen som hanteras av Microsoft. 

Microsoft behandlar allt kundinnehåll som känsligt och sträcker sig långt för att skydda mot förlust och exponering av kunddata. 

För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och upprätthåller en uppsättning robusta dataskyddskontroller och funktioner. 

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisering om ändringar av viktiga Azure-resurser

**Vägledning:** Använd Azure Monitor med Azure-aktivitetsloggen för att skapa aviseringar när ändringar sker i viktiga Azure-resurser, till exempel lastbalanserare som används för viktiga produktionsarbetsbelastningar.

- [Så här skapar du aviseringar för Azure-aktivitetslogghändelser](../azure-monitor/alerts/alerts-activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Benchmark för Azure-säkerhet: Inventering och tillgångshantering.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Använd automatisk tillgångsidentifiering

**Vägledning:** Använd Azure Resource Graph för att söka efter och identifiera alla resurser (till exempel beräkning, lagring, nätverk, portar, protokoll och så vidare) i dina prenumerationer. Azure Resource Manager vi rekommenderar att du skapar och använder aktuella resurser.

Se till att du har rätt (läsbehörighet) i din klientorganisation och räkna upp alla Azure-prenumerationer och -resurser i dina prenumerationer.

- [Skapa frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="62-maintain-asset-metadata"></a>6.2: Underhålla tillgångsmetadata

**Vägledning:** Tillämpa taggar på Azure-resurser med metadata för att organisera logiskt enligt en taxonomi.

- [Skapa och använda taggar](/azure/azure-resource-manager/resource-group-using-tags)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ta bort obehöriga Azure-resurser

**Vägledning:** Använd taggning, hanteringsgrupper och separata prenumerationer när det är lämpligt för att organisera och spåra tillgångar. 

Stämma av inventeringen regelbundet och se till att obehöriga resurser tas bort från dina prenumerationer inom rimlig tid.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definiera och underhålla en förteckning över godkända Azure-resurser

**Vägledning:** Skapa en lista över godkända Azure-resurser efter organisationens behov som du kan använda som en mekanism för att tillåta. Detta gör att din organisation kan registrera alla nyligen tillgängliga Azure-tjänster när de har granskats formellt och godkänts av organisationens typiska processer för säkerhetsutvärdering.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Övervaka för ej godkända Azure-resurser

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i dina prenumerationer.

Fråga efter och identifiera resurser med Azure Resource Graph inom ägda prenumerationer. 

Se till att alla Azure-resurser som finns i miljön godkänns.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Skapa frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="69-use-only-approved-azure-services"></a>6.9: Använd endast godkända Azure-tjänster

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en viss resurstyp med Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

- [Inbyggda Azure-principexempel för virtuellt nätverk](/azure/virtual-network/policy-samples)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning:** Använd villkorlig Azure Active Directory (Azure AD) för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "Blockera åtkomst" för appen "Microsoft Azure Management".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resources Manager](../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fysiskt eller logiskt avsegrera högriskprogram

**Vägledning:** Programvara som krävs för verksamhetsåtgärder, men som kan medföra högre risk för organisationen, bör isoleras inom en egen virtuell dator och/eller ett virtuellt nätverk och vara tillräckligt skyddad med antingen en Azure Firewall eller en nätverkssäkerhetsgrupp.

- [Så här skapar du ett virtuellt nätverk](../virtual-network/quick-create-portal.md)

- [Så här skapar du en nätverkssäkerhetsgrupp med en säkerhetskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security Benchmark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning:** Använda Azure Policy för att skapa anpassade principer för att granska eller framtvinga konfigurationen av dina Azure-resurser. Använd inbyggda Azure Policy definitioner.

Azure Resource Manager kan exportera mallen i JavaScript Object Notation (JSON), som bör granskas för att säkerställa att konfigurationerna uppfyller säkerhetskraven för din organisation.

Exportera Azure Resource Manager-mallar till JavaScript Object Notation-format (JSON) och granska dem regelbundet för att säkerställa att konfigurationerna uppfyller organisationens säkerhetskrav.

Implementera rekommendationer från Security Center som en säker konfigurationsbaslinje för dina Azure-resurser.

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias)

- [Självstudie: Skapa och hantera principer för att framtvinga efterlevnad](../governance/policy/tutorials/create-and-manage.md)

- [Export av en enskild resurs och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Säkerhetsrekommendationer – en referensguide](../security-center/recommendations-reference.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Underhålla säkra Azure-resurskonfigurationer

**Vägledning:** Använd Azure Policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar för alla dina Azure-resurser.  Du kan också använda Azure Resource Manager för att upprätthålla säkerhetskonfigurationen för dina Azure-resurser som krävs av din organisation. 

- [Förstå Azure Policy effekter](../governance/policy/concepts/effects.md)

- [Skapa och hantera principer för att använda kompatibilitet](../governance/policy/tutorials/create-and-manage.md)

- [översikt Azure Resource Manager mallar](../azure-resource-manager/templates/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Lagra konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning:** Använd Azure DevOps för att lagra och hantera kod på ett säkert sätt, till exempel anpassade Azure Policy definitioner, Azure Resource Manager-mallar och önskade tillståndskonfigurationsskript.

Bevilja eller neka behörigheter till specifika användare, inbyggda säkerhetsgrupper eller grupper som definierats i Azure Active Directory (Azure AD) om den är integrerad med Azure DevOps eller i Azure AD om den är integrerad med TFS.

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Om behörigheter och grupper i Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuera konfigurationshanteringsverktyg för Azure-resurser

**Vägledning:** Definiera och implementera standardsäkerhetskonfigurationer för Azure-resurser med hjälp av Azure Policy.  Använd Azure Policy alias för att skapa anpassade principer för att granska eller framtvinga nätverkskonfigurationen för dina Azure-resurser. Implementera inbyggda principdefinitioner som är relaterade till dina Azure Load Balancer resurser.  Använd också Azure Automation för att distribuera konfigurationsändringar. till dina Azure Load Balancer resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här använder du alias](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementera automatisk konfigurationsövervakning för Azure-resurser

**Vägledning:** Använd Security Center för att utföra baslinjesökningar för dina Azure-resurser och Azure Policy aviseringar och granska resurskonfigurationer.

- [Så här åtgärdar du rekommendationer i Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Skapa en guide för incidentsvar

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten. 

- [Så här konfigurerar du arbetsflödesautomation inom Azure Security Center](../security-center/security-center-planning-and-operations-guide.md) 

- [Vägledning om hur du skapar en egen process för svar på säkerhetsincidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft Security Response Center är en incidents uppbyggnad](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Kunden kan också använda NIST:s guide för hantering av säkerhetsincidenter för att underlätta skapandet av en egen plan för incidenthantering](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Skapa en procedur för incidentbedömning och prioritering

**Vägledning:** Security Center tilldelar en allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som ska undersökas först. 

Allvarlighetsgraden baseras på hur säker Security Center är i hitta eller analys som används för att utfärda aviseringen samt förtroendenivån för att det fanns skadlig avsikt bakom aktiviteten som ledde till aviseringen.

Markera prenumerationer med hjälp av taggar och skapa ett namngivningssystem för att identifiera och kategorisera Azure-resurser, särskilt de som bearbetar känsliga data.  

Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="103-test-security-response-procedures"></a>10.3: Testa procedurer för säkerhetssvar

**Vägledning:** Genomför övningar för att regelbundet testa systemets incidentsvarsfunktioner för att skydda dina Azure-resurser. Identifiera svaga punkter och luckor och ändra sedan din åtgärdsplan efter behov. 

- [NIST:s publikation – Guide till test-, tränings- och övningsprogram för IT-planer och IT-funktioner](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Ange kontaktuppgifter för säkerhetsincidenter och konfigurera aviseringsmeddelanden för säkerhetsincidenter

**Vägledning:** Kontaktinformation om säkerhetsincidenter används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en obehörig part. Granska incidenter i efterhand för att säkerställa att problemen är lösta. 

- [Konfigurera en säkerhetskontakt i Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Införliva säkerhetsaviseringar i systemet för incidenter

**Vägledning:** Exportera dina Security Center och rekommendationer med hjälp av funktionen för löpande export för att identifiera risker för Azure-resurser. 

Använd funktionen kontinuerlig export i Security Center som gör att du kan exportera aviseringar och rekommendationer manuellt eller kontinuerligt. 

Använd anslutningsappen Security Center data för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du löpande export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisera svaret på säkerhetsaviseringar

**Vägledning:** Använd funktionen Arbetsflödesautomation i Security Center för att automatiskt utlösa svar på säkerhetsaviseringar och rekommendationer för att skydda dina Azure-resurser.

- [Så här konfigurerar du arbetsflödesautomation i Säkerhet ange](../security-center/workflow-automation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [Azure Security Benchmark: Penetration Tests (Intrångstester) och Red Team Exercises (Red Team-övningar).](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Genomför regelbundna intrångstester av dina Azure-resurser och se till att alla viktiga säkerhetsresultat åtgärdas

**Vägledning:** Följ Microsoft Cloud Penetration Testing Rules of Engagement för att säkerställa att intrångstesterna inte bryter mot Microsofts principer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft. 

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
