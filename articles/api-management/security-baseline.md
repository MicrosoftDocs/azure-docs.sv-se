---
title: Azure-säkerhetsbaslinje för API Management
description: Säkerhetsbaslinjen API Management innehåller procedurvägledning och resurser för implementering av säkerhetsrekommendationerna som anges i Azure Security Benchmark.
author: msmbaldwin
ms.service: api-management
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 488fa4738918849759fbefa86ef1e9730b3b0ed0
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813496"
---
# <a name="azure-security-baseline-for-api-management"></a>Azure-säkerhetsbaslinje för API Management

Den här säkerhetsbaslinjen tillämpar vägledning [från Azure Security Benchmark version 1.0](../security/benchmarks/overview-v1.md) för API Management. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure.
Innehållet grupperas efter de säkerhetskontroller **som definierats** av Azure Security Benchmark och tillhörande vägledning som gäller för API Management. **Kontroller** som inte är API Management har undantagits.

 
Om du vill API Management mappa helt till Azure Security Benchmark kan du se den fullständiga API Management [säkerhetsbaslinjemappningsfilen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Skydda Azure-resurser i virtuella nätverk

**Vägledning:** Azure API Management kan distribueras i ett Azure Virtual Network (Vnet), så att det kan komma åt backend-tjänster i nätverket. Utvecklarportalen och API Management gatewayen kan konfigureras för att vara tillgänglig antingen från Internet (externt) eller endast inom det virtuella nätverket (internt).

- Externt: API Management gatewayen och utvecklarportalen är tillgängliga från det offentliga Internet via en extern lastbalanserare. Gatewayen kan komma åt resurser i det virtuella nätverket.

- Internt: API Management gatewayen och utvecklarportalen är endast tillgängliga inifrån det virtuella nätverket via en intern lastbalanserare. Gatewayen kan komma åt resurser i det virtuella nätverket.

Inkommande och utgående trafik till undernätet där API Management distribueras kan styras med hjälp av nätverkssäkerhetsgruppen.

- [Använda Azure API Management med virtuella nätverk](api-management-using-with-vnet.md)

- [Använda Azure API Management-tjänsten med ett internt virtuellt nätverk](api-management-using-with-internal-vnet.md)

- [Integrera API Management i ett internt VNET med Application Gateway](api-management-howto-integrate-internal-vnet-appgateway.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Övervaka och logga konfiguration och trafik för virtuella nätverk, undernät och nätverksgränssnitt

**Vägledning:** Inkommande och utgående trafik till undernätet där API Management distribueras kan styras med hjälp av nätverkssäkerhetsgrupper (NSG:er). Distribuera en NSG till ditt API Management-undernät och aktivera NSG-flödesloggar och skicka loggar till ett Azure Storage konto för trafikgranskning. Du kan också skicka NSG-flödesloggar till en Log Analytics-arbetsyta och använda Trafikanalys för att ge insikter om trafikflödet i ditt Azure-moln. Några fördelar med Trafikanalys är möjligheten att visualisera nätverksaktivitet och identifiera hotpunkter, identifiera säkerhetshot, förstå trafikflödesmönster och hitta felkonfigurationer i nätverk.

Varning: När du konfigurerar en NSG API Management undernätet finns det en uppsättning portar som måste vara öppna. Om någon av dessa portar inte är API Management kanske inte fungerar korrekt och kan bli otillgänglig.

- [Förstå NSG-konfigurationer för Azure API Management](./api-management-using-with-vnet.md#-common-network-configuration-issues)

- [Så här aktiverar du NSG-flödesloggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Aktivera och använda Trafikanalys](../network-watcher/traffic-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="13-protect-critical-web-applications"></a>1.3: Skydda kritiska webbprogram

**Vägledning:** Om du vill skydda viktiga webb-/HTTP-API:er konfigurerar du API Management i ett Virtual Network (Vnet) i internt läge och konfigurerar en Azure Application Gateway. Application Gateway är en PaaS-tjänst. Den fungerar som en omvänd proxy och tillhandahåller L7-belastningsutjämning, routning, brandvägg för webbaserade program (WAF) och andra tjänster.

Genom att API Management som etablerats i ett internt Vnet med Application Gateway-frontend kan du använda följande scenarier:
- Använd en enda API Management för att exponera alla API:er för både interna konsumenter och externa konsumenter.
- Använd en API Management resurs för att exponera en delmängd av API:er för externa konsumenter.
- Ge ett sätt att växla åtkomst till API Management från det offentliga Internet på och av.

Obs! Den här funktionen är tillgänglig på premium- och utvecklarnivå för API Management.

- [Så här integrerar API Management i ett internt VNET med Application Gateway](api-management-howto-integrate-internal-vnet-appgateway.md)

- [Förstå Azure Application Gateway](../application-gateway/index.yml)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

**Vägledning:** Konfigurera API Management i ett Virtual Network (Vnet) i internt läge och konfigurera en Azure Application Gateway. Application Gateway är en PaaS-tjänst. Den fungerar som en omvänd proxy och tillhandahåller L7-belastningsutjämning, routning, brandvägg för webbaserade program (WAF) och andra tjänster.

Genom att API Management som etablerats i ett internt VNet med Application Gateway-frontend möjliggörs följande scenarier:
- Använd en enda API Management för att exponera alla API:er för både interna konsumenter och externa konsumenter.
- Använd en enda API Management för att exponera en delmängd av API:er för externa konsumenter.
- Ge ett sätt att växla åtkomst till API Management från det offentliga Internet på och av.

Obs! Den här funktionen är tillgänglig på premium- och utvecklarnivå för API Management.

Använd Azure Security Center Integrated Threat Intelligence för att neka kommunikation med kända skadliga eller oanvända Internet-IP-adresser.

- [Så här integrerar API Management i ett internt VNET med Application Gateway](api-management-howto-integrate-internal-vnet-appgateway.md)

- [Förstå Azure Application Gateway](../application-gateway/index.yml)

- [Förstå Azure Security Center Integrated Threat Intelligence](../security-center/azure-defender.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="15-record-network-packets"></a>1.5: Registrera nätverkspaket

**Vägledning:** Inkommande och utgående trafik till undernätet där API Management distribueras kan styras med hjälp av nätverkssäkerhetsgrupper (NSG). Distribuera en NSG till ditt API Management-undernät och aktivera NSG-flödesloggar och skicka loggar till ett Azure Storage-konto för trafikgranskning. Du kan också skicka NSG-flödesloggar till en Log Analytics-arbetsyta och använda Trafikanalys för att ge insikter om trafikflödet i ditt Azure-moln. Vissa fördelar med Trafikanalys är möjligheten att visualisera nätverksaktivitet och identifiera hotpunkter, identifiera säkerhetshot, förstå trafikflödesmönster och hitta felkonfigurationer i nätverk.

Varning: När du konfigurerar en NSG API Management undernätet finns det en uppsättning portar som måste vara öppna. Om någon av dessa portar inte är API Management kanske inte fungerar korrekt och kan bli otillgänglig.

- [Förstå NSG-konfigurationer för Azure API Management](./api-management-using-with-vnet.md#-common-network-configuration-issues)

- [Så här aktiverar du NSG-flödesloggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Aktivera och använda Trafikanalys](../network-watcher/traffic-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuera nätverksbaserade system för intrångsidentifiering/intrångsskydd (IDS/IPS)

**Vägledning:** Konfigurera API Management i ett Virtual Network (Vnet) i internt läge och konfigurera en Azure Application Gateway. Application Gateway är en PaaS-tjänst. Den fungerar som en omvänd proxy och tillhandahåller L7-belastningsutjämning, routning, brandvägg för webbaserade program (WAF) och andra tjänster. Application Gateway WAF ger skydd mot vanliga säkerhetsrisker och sårbarheter och kan köras i följande två lägen:
- Identifieringsläge: Övervakar och loggar alla hotaviseringar. Du kan aktivera loggningsdiagnostik för Application Gateway i avsnittet Diagnostik. Du måste se till att WAF-loggen är markerad och aktiverad. Brandväggen för webbaserade program blockerar inte inkommande begäranden när den används i identifieringsläge.
- Skyddsläge: Blockerar intrång och attacker som identifieras av reglerna. Angriparen får undantaget "403 obehörig åtkomst" och anslutningen stängs. Skyddsläge registrerar sådana attacker i WAF-loggarna.

Genom att API Management som etablerats i ett internt Vnet med Application Gateway-frontend möjliggörs följande scenarier:
- Använd en enda API Management för att exponera alla API:er för både interna konsumenter och externa konsumenter.
- Använd en enda API Management för att exponera en delmängd av API:er för externa konsumenter.
- Ge ett sätt att växla åtkomst till API Management från det offentliga Internet på och av.

Obs! Den här funktionen är tillgänglig på premium- och utvecklarnivå för API Management.

- [Så här integrerar API Management i ett internt VNET med Application Gateway](api-management-howto-integrate-internal-vnet-appgateway.md)

- [Förstå Azure Application Gateway WAF](../web-application-firewall/ag/ag-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Hantera trafik till webbprogram

**Vägledning:** För att hantera trafik som flödar till webb-/HTTP-API:er distribuerar du API Management till en Virtual Network (Vnet) som är associerad App Service-miljön i externt eller internt läge.

I internt läge konfigurerar du en Azure Application Gateway framför API Management. Application Gateway är en PaaS-tjänst. Den fungerar som en omvänd proxy och tillhandahåller L7-belastningsutjämning, routning, brandvägg för webbaserade program (WAF) och andra tjänster. Application Gateway WAF ger skydd mot vanliga säkerhetsrisker och sårbarheter.

Genom att API Management som etablerats i ett internt VNet med Application Gateway-frontend möjliggörs följande scenarier:
- Använd en enda API Management för att exponera alla API:er för både interna konsumenter och externa konsumenter.
- Använd en enda API Management för att exponera en delmängd av API:er för externa konsumenter.
- Ge ett sätt att växla åtkomst till API Management från det offentliga Internet på och av.

Obs! Den här funktionen är tillgänglig på premium- och utvecklarnivå för API Management.

- [Så här exponerar du privata API:er för externa konsumenter](/azure/architecture/example-scenario/apps/publish-internal-apis-externally)

- [Så här använder du API Management i ett VNet](api-management-using-with-vnet.md)

- [Azure Web Application Firewall på Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Förstå Azure Application Gateway](../application-gateway/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexitet och administrativa kostnader för nätverkssäkerhetsregler

**Vägledning:** Använd Virtual Network (Vnet)-tjänsttaggar för att definiera nätverksåtkomstkontroller för nätverkssäkerhetsgrupper (NSG:er) som används API Management undernäten. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttaggnamnet (t.ex. ApiManagement) i lämpligt käll- eller målfält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras.

Varning: När du konfigurerar en NSG API Management undernätet finns det en uppsättning portar som måste vara öppna. Om någon av dessa portar inte är API Management kanske inte fungerar korrekt och kan bli otillgänglig.

- [Förstå och använda tjänsttaggar](../virtual-network/service-tags-overview.md)

- [Portar som krävs för API Management](./api-management-using-with-vnet.md#-common-network-configuration-issues)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Underhålla standardsäkerhetskonfigurationer för nätverksenheter

**Vägledning:** Definiera och implementera standardsäkerhetskonfigurationer för nätverksinställningar relaterade till dina Azure API Management distributioner. Använd Azure Policy alias i namnrymderna "Microsoft.ApiManagement" och "Microsoft.Network" för att skapa anpassade principer för att granska eller framtvinga nätverkskonfiguration för dina Azure API Management-distributioner och relaterade resurser.

Du kan också använda Azure Blueprints för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljöartefakter, till exempel Azure Resource Manager-mallar, rollbaserad åtkomstkontroll i Azure (Azure RBAC) och principer i en enda skissdefinition. Du kan enkelt tillämpa skissen på nya prenumerationer, miljöer och finjustera kontroll och hantering via versionshantering.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du en Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentera trafikkonfigurationsregler

**Vägledning:** Använd taggar för nätverkssäkerhetsgrupper (NSG:er) och andra resurser som rör nätverkssäkerhet och trafikflöde. För enskilda NSG-regler kan du använda fältet "Beskrivning" för att ange affärs behov och/eller varaktighet (osv.) för regler som tillåter trafik till/från ett nätverk.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

- [Så här skapar du en Virtual Network](../virtual-network/quick-create-portal.md)

- [Så här skapar du en NSG med en säkerhetskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

**Vägledning:** Använd Azure-aktivitetsloggen för att övervaka nätverksresurskonfigurationer och identifiera ändringar i nätverksresurser som är associerade med dina Azure API Management distributioner. Skapa aviseringar inom Azure Monitor som utlöses när ändringar av kritiska nätverksresurser sker.

- [Så här visar och hämtar du Händelser i Azure-aktivitetsloggen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Skapa aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [Benchmark för Azure-säkerhet: Loggning och övervakning.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera central hantering av säkerhetsloggar

**Vägledning:** Använd Log Analytics-arbetsytor i Azure Monitor för att fråga och utföra analyser, skicka loggar till Azure Storage för långsiktig/arkiveringslagring eller offlineanalys eller exportera loggar till andra analyslösningar i Azure och någon annanstans med hjälp av Azure Event Hubs. Azure API Management matar ut loggar och mått Azure Monitor som standard. Utförlig loggning kan konfigureras på tjänstomfattande och per API-basis.

Förutom att Azure Monitor kan Azure API Management integreras med en eller flera Azure Application Insights-tjänster. Loggningsinställningar för Application Insights kan konfigureras antingen per tjänst eller per API.

Du kan också aktivera och ta med data för att Azure Sentinel eller en säkerhetsincident- och händelsehantering från tredje part (SIEM).

- [Så här konfigurerar du diagnostikinställningar](../azure-monitor/essentials/diagnostic-settings.md#create-in-azure-portal)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Så här kommer du igång Azure Monitor siem-integrering från tredje part](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Så här skapar du en pipeline för anpassad loggning och analys](api-management-howto-log-event-hubs.md)

- [Integrera med Azure Application Insights](api-management-howto-app-insights.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägledning:** För kontrollplansgranskningsloggning aktiverar du diagnostikinställningar för Azure-aktivitetsloggar och skickar aktivitetsloggar till en Log Analytics-arbetsyta för rapportering och analys, till Azure Storage för långsiktig säkerhet, till Azure Event Hubs för export i andra analyslösningar i Azure och på andra platser. Med hjälp av Azure-aktivitetsloggdata kan du fastställa "vad, vem och när" för alla skrivåtgärder (PUT, POST, DELETE) som utförs på kontrollplansnivå för din Azure API Management-tjänst.

För granskningsloggning på dataplanet ger diagnostikloggar omfattande information om åtgärder och fel som är viktiga för granskning och felsökning. Diagnostikloggar skiljer sig från aktivitetsloggar. Aktivitetsloggar ger insikt i de åtgärder som vidtogs för dina Azure-resurser. Diagnostikloggar ger information om åtgärder som din resurs har vidtagit.

- [Så här aktiverar du diagnostikinställningar för Azure-aktivitetsloggen](../azure-monitor/essentials/activity-log.md)

- [Så här aktiverar du diagnostikinställningar för Azure API Management](./api-management-howto-use-azure-monitor.md#resource-logs)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera kvarhållning av säkerhetslogglagring

**Vägledning:** Inom Azure Monitor anger du kvarhållningsperioden för Log Analytics-arbetsytan enligt organisationens efterlevnadsregler. Använd Azure Storage-konton för långsiktig lagring/arkiveringslagring.

- [Så här anger du parametrar för loggbevarande för Log Analytics-arbetsytor](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Så här arkiverar du loggar till ett Azure Storage konto](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning:** Azure API Management skickar kontinuerligt loggar och mått till Azure Monitor, vilket ger dig en insyn i nästan realtid över dina API:ers tillstånd och hälsotillstånd. Med Azure Monitor och Log Analytics-arbetsytor kan du granska, fråga, visualisera, dirigera, arkivera, konfigurera aviseringar och vidta åtgärder för mått och loggar som kommer från API Management relaterade resurser. Analysera och övervaka loggar för avvikande beteenden och granska resultaten regelbundet.

Du kan också API Management med Azure Application Insights och använda det som primärt eller sekundärt verktyg för övervakning, spårning, rapportering och avisering.

- [Övervaka och granska loggar för Azure API Management](./api-management-howto-use-azure-monitor.md)

- [Så här utför du anpassade frågor i Azure Monitor](../azure-monitor/logs/get-started-queries.md)

- [Förstå Log Analytics-arbetsyta](../azure-monitor/logs/log-analytics-tutorial.md)

- [Integrera med Azure Application Insights](api-management-howto-app-insights.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivera aviseringar för avvikande aktiviteter

**Vägledning:** Aktivera diagnostikinställningar för Azure-aktivitetsloggen samt diagnostikinställningar för dina Azure API Management-instanser och skicka loggarna till en Log Analytics-arbetsyta. Utför frågor i Log Analytics för att söka efter termer, identifiera trender, analysera mönster och ge många andra insikter baserat på insamlade data. Du kan skapa aviseringar baserat på dina Log Analytics-arbetsytefrågor.

Skapa måttaviseringar som meddelar dig när något oväntat inträffar. Du kan till exempel få meddelanden när din Azure API Management-instans överskrider den förväntade toppkapaciteten under en viss tidsperiod eller om det har funnits ett visst antal obehöriga gatewaybegäranden eller fel under en fördefinierad tidsperiod.

Du kan också API Management med Azure Application Insights och använda det som primärt eller sekundärt verktyg för övervakning, spårning, rapportering och avisering.

Om du vill kan du aktivera och ta med data Azure Sentinel eller siem från tredje part.

- [Så här aktiverar du diagnostikinställningar för Azure-aktivitetsloggen](../azure-monitor/essentials/activity-log.md)

- [Så här aktiverar du diagnostikinställningar för Azure API Management](./api-management-howto-use-azure-monitor.md#resource-logs)

- [Så här konfigurerar du en aviseringsregel för Azure API Management](./api-management-howto-use-azure-monitor.md#set-up-an-alert-rule)

- [Så här visar du kapacitetsmått för en Azure API Management-instans](api-management-capacity.md)

- [Integrera med Azure Application Insights](api-management-howto-app-insights.md)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Upprätthålla en förteckning över administrativa konton

**Vägledning:** Upprätthålla en förteckning över konton som har administrativ åtkomst till Azure API Management kontrollplan (Azure Portal).

Azure Active Directory (Azure AD) har inbyggda roller som måste tilldelas explicit och som kan frågas. API Management dessa roller och Role-Based Access Control för att möjliggöra mer information om åtkomsthantering för API Management tjänster och entiteter.

Dessutom API Management en inbyggd administratörsgrupp i API Management användarens system. Grupper i API Management styra synligheten för API:er i utvecklarportalen och medlemmarna i gruppen Administratörer kan se alla API:er.

Följ rekommendationerna Azure Security Center för hantering och underhåll av administrativa konton.

- [Så här använder du rollbaserad åtkomstkontroll i Azure API Management](api-management-role-based-access-control.md)

- [Så här hämtar du en lista över användare under en Azure API Management-instans](/powershell/module/az.apimanagement/get-azapimanagementuser)

- [Så här hämtar du en lista över användare som har tilldelats en katalogroll i Azure AD med PowerShell](/powershell/module/az.resources/get-azroleassignment)

- [Så här hämtar du en katalogrolldefinition i Azure AD med PowerShell](/powershell/module/az.resources/get-azroledefinition)

- [Förstå identitets- och åtkomstrekommendationer från Azure Security Center](../security-center/recommendations-reference.md#identityandaccess-recommendations)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändra standardlösenord i förekommande fall

**Vägledning:** Azure API Management har inte begreppet standardlösenord/nyckel.

Azure API Management-prenumerationer, som är ett sätt att skydda åtkomsten till API:er, har dock ett par genererade prenumerationsnycklar. Kunder kan återskapa prenumerationsnycklarna när som helst.

- [Förstå Azure API Management prenumerationer](api-management-subscriptions.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Använd dedikerade administrativa konton

**Vägledning:** Skapa standardprocedurer för användning av dedikerade administrativa konton. Använd Azure Security Center Identitets- och åtkomsthantering för att övervaka antalet administrativa konton.

För att hjälpa dig att hålla reda på dedikerade administrativa konton kan du dessutom använda rekommendationer från Azure Security Center eller inbyggda Azure-principer, till exempel:
- Det bör finnas fler än en ägare tilldelad till din prenumeration
- Inaktuella konton med ägarbehörighet bör tas bort från prenumerationen
- Externa konton med ägarbehörighet ska tas bort från din prenumeration

- [Så här använder du Azure Security Center för att övervaka identitet och åtkomst (förhandsversion)](../security-center/security-center-identity-access.md)

- [Använda Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory för enkel inloggning (SSO)

**Vägledning:** Azure API Management kan konfigureras för att utnyttja Azure Active Directory (Azure AD) som identitetsprovider för autentisering av användare på utvecklarportalen för att dra nytta av funktionerna för enkel inloggning som erbjuds av Azure AD. När den har konfigurerats kan nya användare av utvecklarportalen välja att följa den förkonfigurerade registreringsprocessen genom att först autentisera via Azure AD och sedan slutföra registreringsprocessen på portalen när den har autentiserats.

- [Auktorisera utvecklarkonton med hjälp av Azure AD i Azure API Management](api-management-howto-aad.md)

Alternativt kan inloggnings-/registreringsprocessen anpassas ytterligare via delegering. Med delegering kan du använda din befintliga webbplats för att hantera utvecklarens inloggning/registrering och prenumeration på produkter, i stället för att använda de inbyggda funktionerna i utvecklarportalen. Det gör att din webbplats kan äga användardata och utföra verifieringen av dessa steg på ett anpassat sätt.

- [Delegera användarregistring och produktprenumeration](api-management-howto-setup-delegation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använd multifaktorautentisering för Azure Active Directory åtkomstbaserad åtkomst

**Vägledning:** Aktivera Azure Active Directory (Azure AD) multifaktorautentisering och följ Azure Security Center rekommendationer för identitets- och åtkomsthantering.

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst inom Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Använd dedikerade datorer (arbetsstationer för privilegierad åtkomst) för alla administrativa uppgifter

**Vägledning:** Använda ARBETSSTATIONER med privilegierad åtkomst (PAW) med multifaktorautentisering som konfigurerats för att logga in på och konfigurera Azure-resurser.

- [Läs mer om arbetsstationer med privilegierad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
  
- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logga och varna om misstänkta aktiviteter från administrativa konton

**Vägledning:** Använd Azure Active Directory (Azure AD) Privileged Identity Management (PIM) för generering av loggar och aviseringar när misstänkt eller osäker aktivitet inträffar i miljön.

Använd dessutom Azure AD-riskidentifiering för att visa aviseringar och rapporter om riskfyllda användarbeteenden.

- [Så här distribuerar du Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Förstå Azure AD-riskidentifiering](../active-directory/identity-protection/overview-identity-protection.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Hantera Azure-resurser från endast godkända platser

**Vägledning:** Använd namngivna platser för villkorsstyrd åtkomst för att tillåta åtkomst till Azure Portal endast från specifika logiska gruppningar av IP-adressintervall eller länder/regioner.

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning:** Om möjligt använder du Azure Active Directory (Azure AD) som centralt system för autentisering och auktorisering. Azure AD skyddar data med hjälp av stark kryptering för vilodata och under överföring. Azure AD lagrar också salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

Konfigurera Din Azure API Management Developer Portal för att autentisera utvecklarkonton med hjälp av Azure AD.

Konfigurera din Azure API Management instans för att skydda dina API:er med hjälp av OAuth 2.0-protokollet med Azure AD.

- [Auktorisera utvecklarkonton med hjälp av Azure AD i Azure API Management](api-management-howto-aad.md)

- [Skydda ett API med hjälp av OAuth 2.0 med Azure AD och API Management](api-management-howto-protect-backend-with-aad.md)

- [Skapa och konfigurera en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Granska och stämma av användaråtkomst regelbundet

**Vägledning:** Azure Active Directory (Azure AD) innehåller loggar som hjälper dig att identifiera inaktuella konton. Kunder kan använda Azure Identity Access Reviews för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Användaråtkomst kan granskas regelbundet för att säkerställa att endast rätt användare har rätt åtkomst.

Kunder kan upprätthålla inventeringen av API Management-användarkonton och stämma av åtkomsten efter behov. I API Management är utvecklare konsumenter av DE API:er som exponeras med API Management. Som standard är nyligen skapade utvecklarkonton aktiva och associerade med gruppen Utvecklare. Utvecklarkonton som är i aktivt tillstånd kan användas för att komma åt alla API:er som de har prenumerationer för.

Administratörer kan skapa anpassade grupper eller utnyttja externa grupper i associerade Azure AD-klientorganisationer. Anpassade och externa grupper kan användas tillsammans med systemgrupper för att välja vilka utvecklare som kan se och komma åt API-produkter.

- [Hantera användarkonton i Azure API Management](api-management-howto-create-or-invite-developers.md)

- [Hämta en lista över API Management användare](/powershell/module/az.apimanagement/get-azapimanagementuser)

- [Så skapar och använder du grupper för att hantera utvecklarkonton i Azure API Management](api-management-howto-create-groups.md)

- [Så här använder du Azure Identity Access Reviews](../active-directory/governance/access-reviews-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning:** Konfigurera din Azure API Management-instans för att autentisera utvecklarkonton med hjälp av Azure Active Directory (Azure AD) som identitetsprovider i Azure API Management.

Konfigurera din Azure API Management instans för att skydda dina API:er med hjälp av OAuth 2.0-protokollet med Azure AD.

Konfigurera JWT-valideringsprincipen för inkommande API-begäranden för att framtvinga förekomsten och giltigheten för en giltig token.

Skapa diagnostikinställningar för Azure AD-användarkonton och skicka granskningsloggar och inloggningsloggar till en Log Analytics-arbetsyta. Konfigurera önskade aviseringar i Log Analytics. Dessutom kan du registrera Log Analytics-arbetsytan för Azure Sentinel eller en SIEM från tredje part.

Konfigurera avancerad övervakning med API Management med hjälp av principen, samla in eventuell ytterligare kontextinformation som krävs för säkerhetsanalys och skicka `log-to-eventhub` den till Azure Sentinel eller SIEM från tredje part.

- [Auktorisera utvecklarkonton med hjälp av Azure AD i Azure API Management](api-management-howto-aad.md)

- [Skydda ett API med hjälp av OAuth 2.0 med Azure AD och API Management](api-management-howto-protect-backend-with-aad.md)

- [Principer för åtkomstbegränsning i API Management](api-management-access-restriction-policies.md)

- [Integrera Azure AD-loggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Så här Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Avancerad övervakning av API:er](api-management-log-to-eventhub-sample.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Avisering om kontots beteendeavvikelse vid inloggning

**Vägledning:** För avvikelse i kontoinloggningsbeteende på kontrollplanet (Azure Portal) använder du funktionerna Azure Active Directory Identity Protection (Azure AD) Identity Protection och riskidentifiering för att konfigurera automatiserade svar på identifierade misstänkta åtgärder relaterade till användaridentiteter. Du kan också mata in data i Azure Sentinel för ytterligare undersökning.

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du riskprinciper för Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ge Microsoft åtkomst till relevanta kunddata under supportscenarier

**Vägledning:** Inte tillgängligt för närvarande; Customer Lockbox stöds för närvarande inte för Azure API Management.

- [Lista över Customer Lockbox tjänster som stöds](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Upprätthålla en förteckning över känslig information

**Vägledning:** Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolera system som lagrar eller bearbetar känslig information

**Vägledning:** Implementera separata prenumerationer och/eller hanteringsgrupper för utveckling, testning och produktion. Azure API Management-instanser ska avgränsas med virtuellt nätverk (VNet)/undernät och taggas på rätt sätt.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

- [Använda Azure API Management med virtuella nätverk](api-management-using-with-vnet.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Övervaka och blockera obehörig överföring av känslig information

**Vägledning:** Inte tillgängligt för närvarande; funktioner för dataidentifiering, klassificering och förlustskydd är för närvarande inte tillgängliga för Azure API Management.

Microsoft hanterar den underliggande infrastrukturen för Azure API Management och har implementerat strikta kontroller för att förhindra förlust eller exponering av kunddata.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Kryptera all känslig information under överföring

**Vägledning:** Anrop till hanteringsplanet görs via Azure Resource Manager över TLS. En giltig JSON-webbtoken (JWT) krävs. Dataplans-anrop kan skyddas med TLS och en av autentiseringsmekanismerna som stöds (till exempel klientcertifikat eller JWT).

- [Förstå dataskydd i Azure API Management](#data-protection)

- [Hantera TLS-inställningar i Azure API Management](api-management-howto-manage-protocols-ciphers.md)

- [Skydda API:er i Azure API Management med Azure Active Directory (Azure AD)](api-management-howto-protect-backend-with-aad.md)

- [Skydda API:er i Azure API Management med Azure AD B2C](howto-protect-backend-frontend-azure-ad-b2c.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Använd ett aktivt identifieringsverktyg för att identifiera känsliga data

**Vägledning:** Inte tillgängligt ännu; funktioner för dataidentifiering, klassificering och förlustskydd är ännu inte tillgängliga för Azure API Management. Tagga Azure API Management tjänster som kan bearbeta känslig information som sådan och implementera tredjepartslösningar om det behövs för efterlevnadsändamål.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt och tar lång tid att skydda mot förlust och exponering av kunddata. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och upprätthåller en uppsättning robusta dataskyddskontroller och -funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Använd Azure RBAC för att styra åtkomsten till resurser 

**Vägledning:** Använda rollbaserad åtkomstkontroll i Azure (Azure RBAC) för att styra åtkomsten till Azure API Management. Azure API Management använder rollbaserad åtkomstkontroll i Azure för att möjliggöra mer information om åtkomsthantering för API Management tjänster och entiteter (till exempel API:er och principer).

- [Så här använder du rollbaserad åtkomstkontroll i Azure API Management](api-management-role-based-access-control.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Använd värdbaserad dataförlustskydd för att framtvinga åtkomstkontroll

**Vägledning:** Ej tillämpligt; den här rekommendationen är avsedd för beräkningsresurser.

Microsoft hanterar den underliggande infrastrukturen för Azure API Management och har implementerat strikta kontroller för att förhindra förlust eller exponering av kunddata.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisering om ändringar av viktiga Azure-resurser

**Vägledning:** Använd Azure Monitor med Azure-aktivitetsloggen för att skapa aviseringar för när ändringar sker i Azure Functions-appar samt andra kritiska eller relaterade resurser.

- [Så här skapar du aviseringar för Händelser i Azure-aktivitetsloggen](../azure-monitor/alerts/alerts-activity-log.md)

- [Så här använder du Azure Monitor azure-aktivitetsloggen i Azure API Management](api-management-howto-use-azure-monitor.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Kör automatiserade sårbarhetsgenomsökningsverktyg

**Vägledning:** Inte tillgängligt för närvarande; sårbarhetsbedömning i Azure Security Center är för närvarande inte tillgänglig för Azure API Management.

Underliggande plattform genomsöks och korrigeras av Microsoft. Granska säkerhetskontroller som är tillgängliga för att minska säkerhetsrisker relaterade till tjänstkonfigurationen.

- [Förstå säkerhetskontroller som är tillgängliga för Azure API Management]()

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Distribuera automatisk lösning för korrigeringshantering av operativsystem

**Vägledning:** Ej tillämpligt; den här rekommendationen är avsedd för beräkningsresurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Distribuera en automatiserad korrigeringshanteringslösning för programvarutitlar från tredje part

**Vägledning:** Ej tillämpligt; den här rekommendationen är avsedd för beräkningsresurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Jämför tillbaka till bakåt-sårbarhetsgenomsökningar

**Vägledning:** Ej tillämpligt; den här rekommendationen är avsedd för beräkningsresurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Använd en riskklassificeringsprocess för att prioritera åtgärder vid identifierade säkerhetsrisker

**Vägledning:** Inte tillgängligt för närvarande; sårbarhetsbedömning i Azure Security Center är för närvarande inte tillgänglig för Azure API Management.

Underliggande plattform genomsöks och korrigeras av Microsoft. Kunden kan granska säkerhetskontroller som är tillgängliga för dem för att minska säkerhetsrisker relaterade till tjänstkonfigurationen.


**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Benchmark för Azure-säkerhet: Inventering och tillgångshantering.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Använd automatisk tillgångsidentifiering

**Vägledning:** Använd Azure Resource Graph för att fråga/identifiera alla resurser (till exempel beräkning, lagring, nätverk, portar och protokoll osv.) i dina prenumerationer. Se till att du har rätt (läs) behörigheter i din klientorganisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan Resource Graph via en Resource Graph rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser i framtiden.

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="62-maintain-asset-metadata"></a>6.2: Underhålla tillgångsmetadata

**Vägledning:** Tillämpa taggar på Azure-resurser som ger metadata för att organisera dem logiskt i en taxonomi.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ta bort obehöriga Azure-resurser

**Vägledning:** Använd taggning, hanteringsgrupper och separata prenumerationer när det är lämpligt för att organisera och spåra Azure-resurser. Stämma av inventeringen regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

Använd dessutom Azure Policy för att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationerna med hjälp av följande inbyggda principdefinitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definiera och underhålla inventering av godkända Azure-resurser

**Vägledning:** Inte tillämpligt; den här rekommendationen är avsedd för beräkningsresurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Övervaka för ej godkända Azure-resurser

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i dina prenumerationer med hjälp av följande inbyggda principdefinitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

Använd Azure Resource Graph att fråga efter/identifiera resurser i sina prenumerationer. Se till att alla Azure-resurser som finns i miljön godkänns.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Övervaka för program som inte är godkända i beräkningsresurser

**Vägledning:** Ej tillämpligt; den här rekommendationen är avsedd för beräkningsresurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Ta bort ej godkända Azure-resurser och program

**Vägledning:** Ej tillämpligt; den här rekommendationen är avsedd för beräkningsresurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="68-use-only-approved-applications"></a>6.8: Använd endast godkända program

**Vägledning:** Ej tillämpligt; den här rekommendationen är avsedd för beräkningsresurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="69-use-only-approved-azure-services"></a>6.9: Använd endast godkända Azure-tjänster

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationerna med hjälp av följande inbyggda principdefinitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en viss resurstyp med Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Upprätthålla en förteckning över godkända programvarutitlar

**Vägledning:** Ej tillämpligt; den här rekommendationen är avsedd för beräkningsresurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning:** Konfigurera villkorsstyrd åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "Blockera åtkomst" för Microsoft Azure-hanteringsappen.

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

- [Rollbaserad åtkomstkontroll i Azure API Management](api-management-role-based-access-control.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Begränsa användarnas möjlighet att köra skript inom beräkningsresurser

**Vägledning:** Ej tillämpligt; den här rekommendationen är avsedd för beräkningsresurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fysiskt eller logiskt avsegrera program med hög risk

**Vägledning:** Inte tillämpligt; den här rekommendationen är avsedd för webbprogram som körs Azure App Service eller beräkningsresurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security Benchmark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning:** Definiera och implementera standardsäkerhetskonfigurationer för din Azure API Management-tjänst med Azure Policy. Använd Azure Policy alias i namnområdet "Microsoft.ApiManagement" för att skapa anpassade principer för att granska eller framtvinga konfigurationen av dina Azure API Management tjänster.

- [Så här visar du tillgängliga Azure Policy-alias](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Upprätta säkra operativsystemkonfigurationer

**Vägledning:** Ej tillämpligt; den här rekommendationen är avsedd för beräkningsresurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Upprätthålla säkra Azure-resurskonfigurationer

**Vägledning:** Definiera och implementera standardsäkerhetskonfigurationer för dina Azure API Management tjänster med Azure Policy. Använd Azure Policy alias i namnområdet "Microsoft.ApiManagement" för att skapa anpassade principer för att granska eller framtvinga konfigurationen av Azure API Management instanser. Använd Azure Policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar för alla dina Azure-resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Förstå Azure Policy effekter](../governance/policy/concepts/effects.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Underhålla säkra operativsystemkonfigurationer

**Vägledning:** Ej tillämpligt; den här rekommendationen är avsedd för beräkningsresurser.

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Lagra konfiguration av Azure-resurser på ett säkert sätt

**Vägledning:** Om du använder anpassade Azure-principdefinitioner använder du Azure DevOps eller Azure Repos för att lagra och hantera din Azure API Management-tjänstkonfiguration på ett säkert sätt.

- [Lagra filer i Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Dokumentation om Azure Repos](/azure/devops/repos/)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Lagra anpassade operativsystemavbildningar på ett säkert sätt

**Vägledning:** Ej tillämpligt; den här rekommendationen är avsedd för beräkningsresurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuera konfigurationshanteringsverktyg för Azure-resurser

**Vägledning:** Definiera och implementera standardsäkerhetskonfigurationer för dina Azure API Management tjänster med Azure Policy. Använd Azure Policy alias i namnområdet "Microsoft.ApiManagement" för att skapa anpassade principer för att granska eller framtvinga konfigurationen av Azure API Management instanser. Använd Azure Policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar för alla dina Azure-resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Förstå Azure Policy effekter](../governance/policy/concepts/effects.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Distribuera konfigurationshanteringsverktyg för operativsystem

**Vägledning:** Ej tillämpligt; den här rekommendationen är avsedd för beräkningsresurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementera automatisk konfigurationsövervakning för Azure-resurser

**Vägledning:** Använd Azure API Management DevOps Resource Kit för att utföra konfigurationshantering för Azure API Management.

Dessutom definierar och implementerar du standardsäkerhetskonfigurationer för dina Azure API Management-tjänster med Azure Policy. Använd Azure Policy alias i namnområdet "Microsoft.ApiManagement" för att skapa anpassade principer för att granska eller framtvinga konfigurationen av Azure API Management instanser. Använd Azure Policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar för dina Azure-resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Förstå Azure Policy effekter](../governance/policy/concepts/effects.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementera automatisk konfigurationsövervakning för operativsystem

**Vägledning:** Inte tillämpligt; den här rekommendationen är avsedd för beräkningsresurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Hantera identiteter på ett säkert och automatiskt sätt

**Vägledning:** Använd hanterad tjänstidentitet som genererats av Azure Active Directory (Azure AD) för att ge din API Management-instans enkel och säker åtkomst till andra Azure AD-skyddade resurser, till exempel Azure Key Vault.

- [Så här skapar du en hanterad identitet för en API Management instans](api-management-howto-use-managed-service-identity.md)

- [Princip för autentisering med hanterad identitet](./api-management-authentication-policies.md#ManagedIdentity)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminera oavsiktlig exponering av autentiseringsuppgifter

**Vägledning:** Implementera skanner för autentiseringsuppgifter för att identifiera autentiseringsuppgifter i koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault.

- [Konfigurera skanner för autentiseringsuppgifter](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security Benchmark: Malware Defense.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Använd centralt hanterad programvara mot skadlig kod

**Vägledning:** Ej tillämpligt; den här rekommendationen är avsedd för beräkningsresurser.

Microsofts program mot skadlig kod är aktiverat på den underliggande värd som stöder Azure-tjänster (till exempel Azure API Management), men den körs inte på kundinnehåll.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Skanna filer i förväg som ska laddas upp till Icke-beräkningsbaserade Azure-resurser

**Vägledning:** Ej tillämpligt; den här rekommendationen är avsedd för icke-beräkningsresurser som är utformade för att lagra data.

Microsofts program mot skadlig kod är aktiverat på den underliggande värd som stöder Azure-tjänster (till exempel Azure API Management), men den körs inte på kundinnehåll.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Se till att program mot skadlig kod och signaturer uppdateras

**Vägledning:** Ej tillämpligt; den här rekommendationen är avsedd för icke-beräkningsresurser som är utformade för att lagra data.

Microsofts program mot skadlig kod är aktiverat på den underliggande värd som stöder Azure-tjänster (till exempel Azure API Management), men den körs inte på kundinnehåll.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Prestandatest för Azure-säkerhet: Dataåterställning.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Säkerställ regelbundna automatiserade säkerhetskopieringar

**Vägledning:** Genom att publicera och hantera dina API:er via Azure API Management utnyttjar du feltolerans och infrastrukturfunktioner som du annars skulle utforma, implementera och hantera manuellt. API Management har stöd för distribution i flera regioner, vilket gör dataplanet ogenomträngligt för regionala fel utan att lägga till några driftskostnader.

Funktionerna för säkerhetskopiering och återställning av tjänster API Management de byggstenar som krävs för att implementera en strategi för haveriberedskap. Säkerhetskopierings- och återställningsåtgärder kan utföras manuellt eller automatiserat.

- [Så här distribuerar API Management dataplanet till flera regioner](api-management-howto-deploy-multi-region.md)

- [Så här implementerar du haveriberedskap med hjälp av säkerhetskopiering och återställning i Azure API Management](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

- [Så här anropar du API Management säkerhetskopieringsåtgärden](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup)

- [Anropa API Management återställningsåtgärd](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utför fullständiga systemsäkerhetskopior och säkerhetskopiera kund hanterade nycklar

**Vägledning:** Säkerhetskopierings- och återställningsåtgärder som tillhandahålls av Azure API Management utför fullständig säkerhetskopiering och återställning av systemet.

Hanterade identiteter kan användas för att hämta certifikat Azure Key Vault för API Management anpassade domännamn. Säkerhetskopiera alla certifikat som lagras i Azure Key Vault.

- [Så här implementerar du haveriberedskap med hjälp av säkerhetskopiering och återställning i Azure API Management](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

- [Säkerhetskopiera Azure Key Vault certifikat](/powershell/module/az.keyvault/backup-azkeyvaultcertificate?preserve-view=true&view=azps-4.8.0)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Verifiera alla säkerhetskopior, inklusive kund hanterade nycklar

**Vägledning:** Validera säkerhetskopior genom att utföra en teståterställning av tjänsten och certifikat från säkerhetskopior.

- [Anropa API Management återställningsåtgärd](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

- [Återställa Azure Key Vault certifikat](/powershell/module/az.keyvault/restore-azkeyvaultcertificate)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Skydda säkerhetskopior och kund hanterade nycklar

**Vägledning:** Azure API Management skriver säkerhetskopior till kundägda Azure Storage konton. Följ Azure Storage säkerhetsrekommendationer för att skydda säkerhetskopieringen.

- [Så här implementerar du haveriberedskap med hjälp av säkerhetskopiering och återställning i Azure API Management](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

- [Säkerhetsrekommendation för Blob Storage](../storage/blobs/security-recommendations.md)

Aktivera Soft-Delete i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.

- [Så här aktiverar du Soft-Delete i Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Skapa en incidentsvarsguide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.

- [Vägledning om hur du skapar en egen process för svar på säkerhetsincidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center är en incidents uppbyggnad](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Använd NIST:s guide för hantering av incidenter vid datorsäkerhet för att underlätta skapandet av en egen plan för incidenthantering](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Skapa en procedur för bedömning och prioritering av incidenter

**Vägledning:** Security Center tilldelar en allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som ska undersökas först. Allvarlighetsgraden baseras på hur säker Security Center är vid upptäckten eller den analys som används för att utfärda aviseringen samt konfidensnivån för att det fanns skadlig avsikt bakom aktiviteten som ledde till aviseringen.

Markera dessutom prenumerationer tydligt (t.ex. produktion, icke-prod) med hjälp av taggar och skapa ett namngivningssystem för att tydligt identifiera och kategorisera Azure-resurser, särskilt de som bearbetar känsliga data. Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="103-test-security-response-procedures"></a>10.3: Testa procedurer för säkerhetssvar

**Vägledning:** Utför övningar för att testa systemets incidentsvarsfunktioner regelbundet för att skydda dina Azure-resurser. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [NIST:s publikation – Guide till test-, tränings- och övningsprogram för IT-planer och IT-funktioner](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Ange kontaktuppgifter för säkerhetsincidenter och konfigurera aviseringsmeddelanden för säkerhetsincidenter

**Vägledning:** Kontaktinformation om säkerhetsincidenter används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en obehörig part. Granska incidenter i efterhand för att säkerställa att problemen är lösta.

- [Ställa in Azure Security Center säkerhetskontakt](../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Införliva säkerhetsaviseringar i systemet för incidenter

**Vägledning:** Exportera dina Azure Security Center och rekommendationer med hjälp av funktionen Kontinuerlig export för att identifiera risker för Azure-resurser. Med löpande export kan du exportera aviseringar och rekommendationer manuellt eller kontinuerligt. Du kan använda anslutningsappen Azure Security Center data för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du löpande export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisera svaret på säkerhetsaviseringar

**Vägledning:** Använd funktionen Arbetsflödesautomation i Azure Security Center att automatiskt utlösa svar via "Logic Apps" om säkerhetsaviseringar och rekommendationer.

- [Så här konfigurerar du arbetsflödesautomation och Logic Apps](../security-center/workflow-automation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [Prestandatest för Azure-säkerhet: Intrångstester och Red Team-övningar.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Genomför regelbundna intrångstester av dina Azure-resurser och se till att alla viktiga säkerhetsresultat åtgärdas

**Vägledning:** Följ Microsofts regler för engagemang för att säkerställa att intrångstester inte bryter mot Microsofts principer: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 .

- [Du hittar mer information om Microsofts strategi och körning av Red Teaming och intrångstestning av live-webbplatser mot Microsofts hanterade molninfrastruktur, tjänster och program här](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)
