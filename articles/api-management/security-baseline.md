---
title: Azures säkerhets bas linje för API Management
description: API Management säkerhets bas linje ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: api-management
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7c5fbbd8cdfd75819bffcfb93fc81900feb4a612
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652376"
---
# <a name="azure-security-baseline-for-api-management"></a>Azures säkerhets bas linje för API Management

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 1,0](../security/benchmarks/overview-v1.md) till API Management. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure.
Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för API Management. **Kontroller** som inte är tillämpliga på API Management har uteslutits.

 
Om du vill se hur API Management helt mappar till Azures säkerhets mätning, se den [fullständiga API Management mappnings filen för säkerhets bas linjen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: Azure API Management kan distribueras i ett azure-Virtual Network (VNet), så att den kan komma åt backend-tjänster i nätverket. Developer-portalen och API Management Gateway kan konfigureras att vara tillgängliga antingen från Internet (extern) eller endast inom VNet (internt).

- Externt: API Management Gateway-och Developer-portalen kan nås från det offentliga Internet via en extern belastningsutjämnare. Gatewayen har åtkomst till resurser i det virtuella nätverket.

- Internt: API Management Gateway-och Developer-portalen är bara tillgängliga i det virtuella nätverket via en intern belastningsutjämnare. Gatewayen har åtkomst till resurser i det virtuella nätverket.

Inkommande och utgående trafik till under nätet där API Management distribueras kan kontrol leras med hjälp av nätverks säkerhets gruppen.

- [Använda Azure API Management med virtuella nätverk](api-management-using-with-vnet.md)

- [Använda Azure API Management-tjänsten med ett internt virtuellt nätverk](api-management-using-with-internal-vnet.md)

- [Integrera API Management i ett internt VNET med Application Gateway](api-management-howto-integrate-internal-vnet-appgateway.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverks gränssnitt

**Vägledning**: inkommande och utgående trafik till under nätet där API Management distribueras kan kontrol leras med hjälp av nätverks säkerhets grupper (NSG: er). Distribuera en NSG till ditt API Management undernät och aktivera NSG Flow-loggar och skicka loggar till ett Azure Storage konto för trafik granskning. Du kan också skicka NSG Flow-loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflöde i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

Varning: när du konfigurerar en NSG på API Management under nätet, finns det en uppsättning portar som måste vara öppna. Om någon av dessa portar är otillgänglig kanske API Management inte fungerar korrekt och kan bli oåtkomlig.

- [Förstå NSG-konfigurationer för Azure API Management](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

- [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: om du vill skydda kritiska webb-API-API: er konfigurerar du API Management inom ett Virtual Network (VNet) i internt läge och konfigurerar en Azure Application Gateway. Application Gateway är en PaaS-tjänst. Den fungerar som en omvänd proxy och ger L7-belastnings utjämning, routning, brand vägg för webbaserade program (WAF) och andra tjänster.

Om du kombinerar API Management som har skapats i ett internt virtuellt nätverk med Application Gateway-frontend kan du använda följande scenarier:
- Använd en enda API Management resurs för att exponera alla API: er för både interna och externa konsumenter.
- Använd en enda API Management resurs för att exponera en delmängd av API: er för externa konsumenter.
- Ger dig ett sätt att byta åtkomst till API Management från det offentliga Internet på och av.

OBS! den här funktionen är tillgänglig på nivån Premium och Developer för API Management.

- [Så här integrerar du API Management i ett internt virtuellt nätverk med Application Gateway](api-management-howto-integrate-internal-vnet-appgateway.md)

- [Förstå Azure Application Gateway](/azure/application-gateway/)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Konfigurera API Management inom ett Virtual Network (VNet) i internt läge och konfigurera en Azure Application Gateway. Application Gateway är en PaaS-tjänst. Den fungerar som en omvänd proxy och ger L7-belastnings utjämning, routning, brand vägg för webbaserade program (WAF) och andra tjänster.

Om du kombinerar API Management som har skapats i ett internt virtuellt nätverk med Application Gateway-frontend kan du använda följande scenarier:
- Använd en enda API Management resurs för att exponera alla API: er för både interna och externa konsumenter.
- Använd en enda API Management resurs för att exponera en delmängd av API: er för externa konsumenter.
- Ger dig ett sätt att byta åtkomst till API Management från det offentliga Internet på och av.

OBS! den här funktionen är tillgänglig på nivån Premium och Developer för API Management.

Använd Azure Security Center integrerad Hot information för att neka kommunikation med kända skadliga eller oanvända Internet-IP-adresser.

- [Så här integrerar du API Management i ett internt virtuellt nätverk med Application Gateway](api-management-howto-integrate-internal-vnet-appgateway.md)

- [Förstå Azure Application Gateway](/azure/application-gateway/)

- [Förstå Azure Security Center integrerad Hot information](../security-center/azure-defender.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="15-record-network-packets"></a>1,5: registrera nätverks paket

**Vägledning**: inkommande och utgående trafik till under nätet där API Management distribueras kan kontrol leras med hjälp av nätverks säkerhets grupper (NSG). Distribuera en NSG till ditt API Management undernät och aktivera NSG Flow-loggar och skicka loggar till ett Azure Storage konto för trafik granskning. Du kan också skicka NSG Flow-loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflöde i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

Varning: när du konfigurerar en NSG på API Management under nätet, finns det en uppsättning portar som måste vara öppna. Om någon av dessa portar är otillgänglig kanske API Management inte fungerar korrekt och kan bli oåtkomlig.

- [Förstå NSG-konfigurationer för Azure API Management](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

- [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera nätverksbaserade intrångs identifiering/system för skydd mot intrång (ID/IP-adresser)

**Vägledning**: Konfigurera API Management inom ett Virtual Network (VNet) i internt läge och konfigurera en Azure Application Gateway. Application Gateway är en PaaS-tjänst. Den fungerar som en omvänd proxy och ger L7-belastnings utjämning, routning, brand vägg för webbaserade program (WAF) och andra tjänster. Application Gateway WAF ger skydd mot vanliga sårbarheter och säkerhets risker och kan köras i följande två lägen:
- Identifierings läge: övervakar och loggar alla hot aviseringar. Du kan aktivera Logging Diagnostics för Application Gateway i avsnittet diagnostik. Du måste se till att WAF-loggen är markerad och aktive rad. Brand väggen för webbaserade program blockerar inte inkommande förfrågningar när den körs i identifierings läge.
- Skydds läge: blockerar intrång och attacker som reglerna identifierar. Angriparen får ett undantag för "403 obehörig åtkomst" och anslutningen stängs. I skydds läget registreras sådana attacker i WAF-loggarna.

Om du kombinerar API Management som har skapats i ett internt virtuellt nätverk med Application Gateway-frontend kan du använda följande scenarier:
- Använd en enda API Management resurs för att exponera alla API: er för både interna och externa konsumenter.
- Använd en enda API Management resurs för att exponera en delmängd av API: er för externa konsumenter.
- Ger dig ett sätt att byta åtkomst till API Management från det offentliga Internet på och av.

OBS! den här funktionen är tillgänglig på nivån Premium och Developer för API Management.

- [Så här integrerar du API Management i ett internt virtuellt nätverk med Application Gateway](api-management-howto-integrate-internal-vnet-appgateway.md)

- [Förstå Azure Application Gateway-WAF](../web-application-firewall/ag/ag-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: om du vill hantera trafik som flödar till webb-/http-API: er distribuerar API Management till en Virtual Network (VNet) som är kopplad till App Service-miljön i externt eller internt läge.

I internt läge konfigurerar du en Azure Application Gateway framför API Management. Application Gateway är en PaaS-tjänst. Den fungerar som en omvänd proxy och ger L7-belastnings utjämning, routning, brand vägg för webbaserade program (WAF) och andra tjänster. Application Gateway WAF ger skydd mot vanliga sårbarheter och säkerhets risker.

Om du kombinerar API Management som har skapats i ett internt virtuellt nätverk med Application Gateway-frontend kan du använda följande scenarier:
- Använd en enda API Management resurs för att exponera alla API: er för både interna och externa konsumenter.
- Använd en enda API Management resurs för att exponera en delmängd av API: er för externa konsumenter.
- Ger dig ett sätt att byta åtkomst till API Management från det offentliga Internet på och av.

OBS! den här funktionen är tillgänglig på nivån Premium och Developer för API Management.

- [Så här visar du privata API: er till externa konsumenter](/azure/architecture/example-scenario/apps/publish-internal-apis-externally)

- [Använda API Management i ett VNet](api-management-using-with-vnet.md)

- [Azure Web Application-brandvägg på Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Förstå Azure Application Gateway](/azure/application-gateway/overview)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: Använd Virtual Network (VNet) service taggar för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper (NSG: er) som används i dina API Management-undernät. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet (t. ex. API Management) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

Varning: när du konfigurerar en NSG på API Management under nätet, finns det en uppsättning portar som måste vara öppna. Om någon av dessa portar är otillgänglig kanske API Management inte fungerar korrekt och kan bli oåtkomlig.

- [Förstå och använda service märken](../virtual-network/service-tags-overview.md)

- [Portar som krävs för API Management](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för nätverks inställningar som är relaterade till dina Azure API Management-distributioner. Använd Azure Policy alias i namn områdena "Microsoft. API Management" och "Microsoft. Network" om du vill skapa anpassade principer för att granska eller tillämpa nätverks konfiguration för dina Azure API Management-distributioner och relaterade resurser.

Du kan också använda Azure-ritningar för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljö artefakter, till exempel Azure Resource Manager mallar, rollbaserad åtkomst kontroll i Azure (Azure RBAC) och principer i en enda skiss definition. Du kan enkelt använda skissen för nya prenumerationer, miljöer och finjustera kontroll och hantering genom versions hantering.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du en Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för nätverks säkerhets grupper (NSG: er) och andra resurser som rör nätverks säkerhets-och trafikflödet. För enskilda NSG-regler kan du använda fältet Beskrivning för att ange affärs behov och/eller varaktighet (osv.) för alla regler som tillåter trafik till/från ett nätverk.

- [Skapa och använda Taggar](/azure/azure-resource-manager/resource-group-using-tags)

- [Så här skapar du en Virtual Network](../virtual-network/quick-create-portal.md)

- [Så här skapar du en NSG med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar av nätverks resurser som är associerade med dina Azure API Management-distributioner. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks resurser sker.

- [Visa och hämta Azure aktivitets logg händelser](/azure/azure-monitor/platform/activity-log-view)

- [Så här skapar du aviseringar i Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets benchmark för Azure: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: i Azure Monitor använder du Log Analytics arbets ytor (er) för att fråga och utföra analyser, skicka loggar till Azure Storage för långsiktig/arkivera lagring eller offline-analys, eller exportera loggar till annan analys lösning på Azure och någon annan stans med Azure Event Hubs. Azure API Management matar ut loggar och mått till Azure Monitor som standard. Utförlig loggning kan konfigureras för en tjänst och per-API-basis.

Förutom Azure Monitor kan Azure API Management integreras med en eller flera Azure Application Insights-tjänster. Loggnings inställningar för Application Insights kan konfigureras antingen per tjänst eller per-API-basis.

Du kan också aktivera och inaktivera data till Azure Sentinel eller en säkerhets incident och händelse hantering (SIEM) från tredje part.

- [Konfigurera diagnostikinställningar](/azure/azure-monitor/platform/diagnostic-settings#create-in-azure-portal)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Komma igång med Azure Monitor och SIEM-integrering från tredje part](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Så här skapar du en anpassad loggnings-och analys-pipeline](api-management-howto-log-event-hubs.md)

- [Integrera med Azure Application Insights](api-management-howto-app-insights.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: för gransknings loggning i kontroll planet aktiverar du Azure Activity Log-diagnostikinställningar och skickar aktivitets loggar till en Log Analytics arbets yta för rapportering och analys, för att Azure Storage för långsiktigt säkert skydd på Azure Event Hubs för export i andra analys lösningar på Azure och på andra platser. Med hjälp av Azures aktivitets logg data kan du fastställa "vad, vem och när" för alla Skriv åtgärder (skicka, posta och ta bort) som utförs på kontroll Plans nivån för din Azure API Management-tjänst.

För gransknings loggning av data planet ger diagnostikloggar omfattande information om åtgärder och fel som är viktiga för granskning och fel söknings syfte. Diagnostikloggar skiljer sig från aktivitetsloggar. Aktivitetsloggar ger insikt i de åtgärder som vidtogs för dina Azure-resurser. Diagnostikloggar ger information om åtgärder som din resurs har vidtagit.

- [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](/azure/azure-monitor/platform/activity-log)

- [Så här aktiverar du diagnostikinställningar för Azure API Management](/Azure/api-management/api-management-howto-use-azure-monitor#resource-logs)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: i Azure Monitor ställer du in Log Analytics arbets ytans kvarhållningsperiod enligt organisationens regler för efterlevnad. Använd Azure Storage konton för långsiktig/Arkiv lagring.

- [Ange logg lagrings parametrar för Log Analytics arbets ytor](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Så här arkiverar du loggar till ett Azure Storage konto](/azure/azure-monitor/platform/resource-logs#send-to-azure-storage)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: Azure API Management genererar kontinuerligt loggar och mät värden till Azure Monitor, vilket ger dig en nära real tids insyn i dina API: er tillstånd och hälsa. Med Azure Monitor och Log Analytics arbets ytor kan du granska, fråga, visualisera, cirkulera, arkivera, konfigurera aviseringar och vidta åtgärder för mått och loggar som kommer från API Management och relaterade resurser. Analysera och övervaka loggar för avvikande beteenden och granska resultaten regelbundet.

Du kan också integrera API Management med Azure Application insikter och använda den som primär eller sekundär övervaknings-, spårnings-, rapporterings-och aviserings verktyg.

- [Så här övervakar och granskar du loggar för Azure API Management](/Azure/api-management/api-management-howto-use-azure-monitor)

- [Så här utför du anpassade frågor i Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

- [Förstå Log Analytics arbets yta](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Integrera med Azure Application Insights](api-management-howto-app-insights.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: Aktivera diagnostiska inställningar för Azure aktivitets logg samt diagnostikinställningar för Azure API Management-instanser och skicka loggarna till en Log Analytics arbets yta. Utför frågor i Log Analytics för att söka efter termer, identifiera trender, analysera mönster och tillhandahålla många andra insikter baserat på insamlade data. Du kan skapa aviseringar baserat på Log Analytics arbets ytans frågor.

Skapa mått aviseringar för att meddela dig när något oväntade inträffar. Du kan till exempel få meddelanden när din Azure API Management-instans har överskridit den förväntade högsta kapaciteten under en viss tids period eller om det har uppstått ett visst antal otillåtna Gateway-begäranden eller fel under en fördefinierad tids period.

Du kan också integrera API Management med Azure Application insikter och använda den som primär eller sekundär övervaknings-, spårnings-, rapporterings-och aviserings verktyg.

Alternativt kan du aktivera och fordonsbaserad information till Azure Sentinel eller en SIEM från tredje part.

- [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](/azure/azure-monitor/platform/activity-log)

- [Så här aktiverar du diagnostikinställningar för Azure API Management](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor#resource-logs)

- [Så här konfigurerar du en varnings regel för Azure API Management](/Azure/api-management/api-management-howto-use-azure-monitor#set-up-an-alert-rule-for-unauthorized-request)

- [Visa kapacitets mått för en Azure API Management-instans](api-management-capacity.md)

- [Integrera med Azure Application Insights](api-management-howto-app-insights.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security benchmark: identitet och Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: underhålla en inventering av konton som har administrativ åtkomst till Azure API Management kontroll planet (Azure Portal).

Azure Active Directory (Azure AD) har inbyggda roller som måste tilldelas explicit och som kan frågas. API Management är beroende av dessa roller och Role-Based Access Control för att möjliggöra detaljerad åtkomst hantering för API Management tjänster och entiteter.

API Management innehåller dessutom en inbyggd administratörs grupp i API Managements användar system. Grupper i API Management kontroll synlighet för API: er i Developer-portalen och medlemmar i gruppen Administratörer kan se alla API: er.

Följ rekommendationer från Azure Security Center för hantering och underhåll av administrativa konton.

- [Så här använder du rollbaserad åtkomstkontroll i Azure API Management](api-management-role-based-access-control.md)

- [Hämta en lista med användare under en Azure API Management-instans](/powershell/module/az.apimanagement/get-azapimanagementuser)

- [Så här hämtar du en lista över användare som har tilldelats en katalog roll i Azure AD med PowerShell](/powershell/module/az.resources/get-azroleassignment)

- [Så här hämtar du en katalog Rolls definition i Azure AD med PowerShell](/powershell/module/az.resources/get-azroledefinition)

- [Förstå identitets-och åtkomst rekommendationer från Azure Security Center](https://docs.microsoft.com/azure/security-center/recommendations-reference#identityandaccess-recommendations)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: Azure API Management har inte konceptet standard lösen ord/nyckel.

Azure API Management-prenumerationer, som är ett sätt att skydda åtkomsten till API: er, kommer dock att ha ett par med genererade prenumerations nycklar. Kunder kan när som helst återskapa prenumerations nycklarna.

- [Förstå Azure API Management-prenumerationer](api-management-subscriptions.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton. Använd Azure Security Center identitets-och åtkomst hantering för att övervaka antalet administrativa konton.

För att hjälpa dig att hålla koll på dedikerade administrativa konton kan du dessutom använda rekommendationer från Azure Security Center eller inbyggda Azure-principer, t. ex.:
- Det bör finnas fler än en ägare som tilldelats din prenumeration
- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med ägar behörigheter bör tas bort från din prenumeration

- [Använda Azure Security Center för att övervaka identitet och åtkomst (för hands version)](../security-center/security-center-identity-access.md)

- [Använda Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Använd Azure Active Directory enkel inloggning (SSO)

**Vägledning**: Azure API Management kan konfigureras för att utnyttja Azure Active Directory (Azure AD) som identitets leverantör för att autentisera användare på Developer-portalen för att dra nytta av de SSO-funktioner som erbjuds av Azure AD. När den har kon figurer ATS kan nya utvecklares Portal användare välja att följa den färdiga registrerings processen genom att först autentisera via Azure AD och sedan slutföra registrerings processen på portalen när den har autentiserats.

- [Auktorisera utvecklares konton med Azure AD i Azure API Management](api-management-howto-aad.md)

Du kan också anpassa inloggnings-och registrerings processen ytterligare genom delegering. Med delegering kan du använda din befintliga webbplats för att hantera utvecklares inloggning/registrering och prenumeration på produkter, i stället för att använda de inbyggda funktionerna i Developer-portalen. Det gör att din webbplats kan äga användar data och utföra valideringen av dessa steg på ett anpassat sätt.

- [Delegera användarregistring och produktprenumeration](api-management-howto-setup-delegation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory-baserad åtkomst

**Vägledning**: Aktivera Azure Active Directory (Azure AD) multifaktorautentisering och Azure Security Center Följ rekommendationerna för identitets-och åtkomst hantering.

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd Privileged Access-arbetsstationer (Paw) med multifaktorautentisering konfigurerad för att logga in på och konfigurera Azure-resurser.

- [Lär dig mer om arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
  
- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: Använd Azure Active Directory (Azure AD) PRIVILEGED Identity Management (PIM) för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön.

Dessutom kan du använda Azure AD-farlighets identifiering för att visa aviseringar och rapporter om riskfyllda användar beteenden.

- [Distribuera Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Förstå identifieringar av Azure AD-risker](/azure/active-directory/reports-monitoring/concept-risk-events)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Använd villkorlig åtkomst med namngivna platser för att tillåta åtkomst till Azure Portal från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner.

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: när det är möjligt använder Azure Active Directory (Azure AD) som central autentiserings-och auktoriserings system. Azure AD skyddar data med stark kryptering för data i vila och under överföring. Azure AD innehåller även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

Konfigurera Azure API Management Developer-portalen för att autentisera utvecklares konton med hjälp av Azure AD.

Konfigurera Azure API Management-instansen för att skydda dina API: er genom att använda OAuth 2,0-protokollet med Azure AD.

- [Så här auktoriserar du utvecklares konton med Azure AD i Azure API Management](api-management-howto-aad.md)

- [Så här skyddar du ett API med hjälp av OAuth 2,0 med Azure AD och API Management](api-management-howto-protect-backend-with-aad.md)

- [Skapa och konfigurera en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure Active Directory (Azure AD) innehåller loggar som hjälper till att identifiera inaktuella konton. Kunder kan använda Azure Identity Access-granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användar åtkomst kan granskas regelbundet för att säkerställa att endast rätt användare fortsätter att ha lämplig åtkomst.

Kunder kan underhålla API Management användar konton och synkronisera åtkomst efter behov. I API Management är utvecklare konsumenter av de API: er som exponeras med API Management. Som standard är nya skapade utvecklarresurser aktiva och kopplade till gruppen utvecklare. De utvecklar konton som är i ett aktivt tillstånd kan användas för att få åtkomst till alla API: er som de har prenumerationer för.

Administratörer kan skapa anpassade grupper eller utnyttja externa grupper i tillhör ande Azure AD-klienter. Anpassade och externa grupper kan användas tillsammans med systemgrupper för att välja vilka utvecklare som kan se och komma åt API-produkter.

- [Hantera användarkonton i Azure API Management](api-management-howto-create-or-invite-developers.md)

- [Hämta en lista över API Management användare](/powershell/module/az.apimanagement/get-azapimanagementuser)

- [Så skapar och använder du grupper för att hantera utvecklarkonton i Azure API Management](api-management-howto-create-groups.md)

- [Så här använder du granskningar av Azure Identity Access](../active-directory/governance/access-reviews-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: konfigurera Azure API Management-instansen för att autentisera utvecklarens konton genom att använda Azure Active Directory (Azure AD) som identitets leverantör i Azure API Management.

Konfigurera Azure API Management-instansen för att skydda dina API: er genom att använda OAuth 2,0-protokollet med Azure AD.

Konfigurera JWT-validerings policy till inkommande API-begäranden för att säkerställa att en giltig token finns och är giltig.

Skapa diagnostiska inställningar för Azure AD-användarkonton och skicka gransknings loggarna och inloggnings loggarna till en Log Analytics-arbetsyta. Konfigurera önskade aviseringar inom Log Analytics. Dessutom kan du publicera Log Analytics arbets ytan till Azure Sentinel eller en SIEM från tredje part.

Konfigurera avancerad övervakning med API Management med hjälp av `log-to-eventhub` principen, samla in eventuell ytterligare kontext information som krävs för säkerhets analys och skicka till Azure Sentinel eller Siem från tredje part.

- [Så här auktoriserar du utvecklares konton med Azure AD i Azure API Management](api-management-howto-aad.md)

- [Så här skyddar du ett API med hjälp av OAuth 2,0 med Azure AD och API Management](api-management-howto-protect-backend-with-aad.md)

- [Principer för åtkomstbegränsning i API Management](api-management-access-restriction-policies.md)

- [Så här integrerar du Azure AD-loggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Aktivera Azure-kontroll på kort](../sentinel/quickstart-onboard.md)

- [Avancerad övervakning av API: er](api-management-log-to-eventhub-sample.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: varning vid inloggnings beteende för konto

**Vägledning**: för konto inloggning beteende avvikelse i kontroll planet (Azure Portal) använder du Azure Active Directory (Azure AD) identitets skydd och funktioner för identifiering av risker för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter. Du kan också mata in data i Azure Sentinel för ytterligare undersökning.

- [Så visar du riskfyllda inloggningar för Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: inte tillgänglig för närvarande. Customer Lockbox stöds för närvarande inte för Azure API Management.

- [Lista över Customer Lockbox tjänster som stöds](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information.

- [Skapa och använda Taggar](/azure/azure-resource-manager/resource-group-using-tags)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera separata prenumerationer och/eller hanterings grupper för utveckling, testning och produktion. Azure API Management-instanser ska avgränsas av Virtual Network (VNet)/Subnet och taggas på lämpligt sätt.

- [Så här skapar du ytterligare Azure-prenumerationer](/azure/billing/billing-create-subscription)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](/azure/azure-resource-manager/resource-group-using-tags)

- [Använda Azure API Management med virtuella nätverk](api-management-using-with-vnet.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: inte tillgänglig för närvarande. funktionerna för data identifiering, klassificering och förlust av förlust är inte tillgängliga för Azure API Management.

Microsoft hanterar den underliggande infrastrukturen för Azure API Management och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: hanterings Plans anrop görs via Azure Resource Manager via TLS. En giltig JSON Web token (JWT) krävs. Data Plans anrop kan skyddas med TLS och en av de autentiseringsmekanismer som stöds (till exempel klient certifikat eller JWT).

- [Förstå data skydd i Azure API Management](/azure/api-management/api-management-security-controls#data-protection)

- [Hantera TLS-inställningar i Azure API Management](api-management-howto-manage-protocols-ciphers.md)

- [Skydda API: er i Azure API Management med Azure Active Directory (Azure AD)](api-management-howto-protect-backend-with-aad.md)

- [Skydda API: er i Azure API Management med Azure AD B2C](howto-protect-backend-frontend-azure-ad-b2c.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: ännu inte tillgänglig; funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure API Management. Tagga Azure API Management-tjänster som kan bearbeta känslig information som sådan och implementera en lösning från tredje part om det krävs för efterlevnad.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser 

**Vägledning**: Använd rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att kontrol lera åtkomsten till Azure API Management. Azure API Management förlitar sig på rollbaserad åtkomst kontroll i Azure för att möjliggöra detaljerad åtkomst hantering för API Management tjänster och entiteter (till exempel API: er och principer).

- [Så här använder du rollbaserad åtkomst kontroll i Azure API Management](api-management-role-based-access-control.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

Microsoft hanterar den underliggande infrastrukturen för Azure API Management och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i produktions Azure Functions appar samt andra kritiska eller relaterade resurser.

- [Så här skapar du aviseringar för Azure aktivitets logg händelser](/azure/azure-monitor/platform/alerts-activity-log)

- [Använda Azure Monitor och Azure aktivitets logg i Azure API Management](api-management-howto-use-azure-monitor.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security benchmark: sårbarhet Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: inte tillgänglig för närvarande. sårbarhets bedömning i Azure Security Center är för närvarande inte tillgängligt för Azure API Management.

Underliggande plattform som genomsöks och korrigeras av Microsoft. Granska säkerhets kontroller som är tillgängliga för att minska säkerhets risker för tjänst konfiguration.

- [Förstå säkerhets kontroller som är tillgängliga för Azure API Management](/azure/api-management/api-management-security-controls)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Distribuera automatiserad korrigerings hanterings lösning för program varu titlar från tredje part

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: inte tillgänglig för närvarande. sårbarhets bedömning i Azure Security Center är för närvarande inte tillgängligt för Azure API Management.

Underliggande plattform som genomsöks och korrigeras av Microsoft. Kunden att granska säkerhets kontroller som är tillgängliga för att minska säkerhets risker för tjänst konfigurationen.

- [Förstå säkerhets kontroller som är tillgängliga för Azure API Management](/azure/api-management/api-management-security-controls)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga/identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk, portar och protokoll osv.) i din prenumeration (er). Se till att du har rätt (Läs) behörigheter i din klient organisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via resurs diagram, rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser som går framåt.

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser som ger metadata till att logiskt organisera dem i en taxonomi.

- [Skapa och använda Taggar](/azure/azure-resource-manager/resource-group-using-tags)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer om det behövs för att organisera och spåra Azure-resurser. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

Använd dessutom Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

- [Så här skapar du ytterligare Azure-prenumerationer](/azure/billing/billing-create-subscription)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](/azure/azure-resource-manager/resource-group-using-tags)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla inventering av godkända Azure-resurser

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i dina prenumerationer med hjälp av följande inbyggda princip definitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

Använd Azure Resource Graph för att fråga/identifiera resurser i sina prenumerationer. Se till att alla Azure-resurser som finns i miljön är godkända.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: underhåll en inventering av godkända program varu titlar

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Konfigurera villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

- [Rollbaserad åtkomst kontroll i Azure API Management](api-management-role-based-access-control.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security benchmark: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: definiera och implementera standardkonfigurationer för dina Azure API Management-tjänster med Azure policy. Använd Azure Policy alias i namn området "Microsoft. API Management" om du vill skapa anpassade principer för granskning eller tillämpa konfigurationen för Azure API Management-tjänsterna.

- [Visa tillgängliga Azure Policy alias](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: definiera och implementera standardkonfigurationer för dina Azure API Management-tjänster med Azure policy. Använd Azure Policy alias i namn området "Microsoft. API Management" om du vill skapa anpassade principer för granskning eller tillämpa konfigurationen av Azure API Management-instanser. Använd Azure policy [Deny] och [distribuera om det inte finns] för att genomdriva säkra inställningar i dina Azure-resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: om du använder anpassade definitioner av Azure-principer använder du Azure DevOps eller Azure databaser för att lagra och hantera din Azure API Management-tjänstekonfiguration på ett säkert sätt.

- [Så här lagrar du filer i Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Dokumentation om Azure databaser](/azure/devops/repos/)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: definiera och implementera standardkonfigurationer för dina Azure API Management-tjänster med Azure policy. Använd Azure Policy alias i namn området "Microsoft. API Management" om du vill skapa anpassade principer för granskning eller tillämpa konfigurationen av Azure API Management-instanser. Använd Azure policy [Deny] och [distribuera om det inte finns] för att genomdriva säkra inställningar i dina Azure-resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Distribuera konfigurations hanterings verktyg för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: använd Azure API Management DevOps Resource Kit för att utföra konfigurations hantering för Azure API Management.

Definiera och implementera standard säkerhetskonfigurationer för Azure API Management-tjänster med Azure Policy. Använd Azure Policy alias i namn området "Microsoft. API Management" om du vill skapa anpassade principer för granskning eller tillämpa konfigurationen av Azure API Management-instanser. Använd Azure policy [Deny] och [distribuera om det inte finns] för att genomdriva säkra inställningar i dina Azure-resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: Använd hanterad tjänstidentitet som genereras av Azure Active Directory (Azure AD) för att tillåta din API Management-instans att enkelt och säkert komma åt andra Azure AD-skyddade resurser, till exempel Azure Key Vault.

- [Så här skapar du en hanterad identitet för en API Management instans](api-management-howto-use-managed-service-identity.md)

- [Princip för att autentisera med hanterad identitet](https://docs.microsoft.com/azure/api-management/api-management-authentication-policies#ManagedIdentity)

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

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som har stöd för Azure-tjänster (till exempel Azure API Management), men det körs inte på kund innehåll.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för icke-Compute-resurser som är utformade för att lagra data.

Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som har stöd för Azure-tjänster (till exempel Azure API Management), men det körs inte på kund innehåll.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för skadlig program vara uppdateras

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för icke-Compute-resurser som är utformade för att lagra data.

Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som har stöd för Azure-tjänster (till exempel Azure API Management), men det körs inte på kund innehåll.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Azure Security benchmark: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Säkerställ regelbunden automatisk säkerhets kopiering

**Vägledning**: genom att publicera och hantera dina API: er via Azure API Management drar du nytta av fel tolerans och infrastruktur funktioner som du annars kan utforma, implementera och hantera manuellt. API Management stöder distribution i flera regioner, vilket gör att data planet är ogenomträngligt för regionala haverier utan att lägga till någon drifts kostnad.

Säkerhets kopierings-och återställnings funktionerna i API Management ger nödvändiga Bygg stenar för att implementera en katastrof återställnings strategi. Säkerhets kopierings-och återställnings åtgärder kan utföras manuellt eller automatiseras.

- [Så här distribuerar du API Management data plan till flera regioner](api-management-howto-deploy-multi-region.md)

- [Så här implementerar du haveriberedskap med hjälp av säkerhetskopiering och återställning i Azure API Management](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

- [Så här anropar du säkerhets kopierings åtgärden för API Management](/rest/api/apimanagement/2019-01-01/apimanagementservice/backup)

- [Så här anropar du åtgärden API Management återställning](/rest/api/apimanagement/2019-01-01/apimanagementservice/restore)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: säkerhets kopierings-och återställnings åtgärder som tillhandahålls av Azure API Management utföra fullständig säkerhets kopiering och återställning av systemet.

Hanterade identiteter kan användas för att hämta certifikat från Azure Key Vault för API Management anpassade domän namn. Säkerhetskopiera alla certifikat som lagras i Azure Key Vault.

- [Så här implementerar du haveriberedskap med hjälp av säkerhetskopiering och återställning i Azure API Management](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

- [Säkerhetskopiera Azure Key Vault certifikat](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-4.8.0&amp;preserve-view=true)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: validera säkerhets kopieringar genom att utföra en test återställning av tjänsten och certifikat från säkerhets kopior.

- [Så här anropar du åtgärden API Management återställning](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

- [Återställa Azure Key Vault-certifikat](/powershell/module/az.keyvault/restore-azkeyvaultcertificate)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: Azure API Management skriver säkerhets kopior till kundägda Azure Storage-konton. Följ Azure Storage säkerhets rekommendationer för att skydda säkerhets kopian.

- [Så här implementerar du haveriberedskap med hjälp av säkerhetskopiering och återställning i Azure API Management](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

- [Säkerhets rekommendation för Blob Storage](../storage/blobs/security-recommendations.md)

Aktivera Soft-Delete i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.

- [Så här aktiverar du Soft-Delete i Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.

- [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Utnyttja NISTs hanterings guide för dator säkerhet för att hjälpa dig att skapa en egen incident svars plan](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller analysera den som används för att utfärda aviseringen samt den konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Dessutom är det tydligt att markera prenumerationer (t. ex. produktion, icke-Prod.) med hjälp av taggar och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser, särskilt för bearbetning av känsliga data. Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](/azure/azure-resource-manager/resource-group-using-tags)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems funktioner för incident svar på en vanlig takt för att hjälpa till att skydda dina Azure-resurser. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [NIST-guide för att testa, träna och träna program för IT-planer och funktioner](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en olagligt eller obehörig part. Granska incidenter när du är säker på att problemen är lösta.

- [Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export för att identifiera risker för Azure-resurser. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för arbets flödes automatisering i Azure Security Center att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer.

- [Konfigurera automatisering av arbets flöden och Logic Apps](../security-center/workflow-automation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [övningen för Azure Security benchmark: inträngande tester och röda team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 .

- [Du hittar mer information om Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft Managed Cloud Infrastructure, tjänster och program](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
