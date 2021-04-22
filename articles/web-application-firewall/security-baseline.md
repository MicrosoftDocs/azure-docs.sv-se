---
title: Azure-säkerhetsbaslinje för Azure Web Application Firewall
description: Säkerhetsbaslinjen Azure Web Application Firewall innehåller procedurvägledning och resurser för implementering av säkerhetsrekommendationerna som anges i Azure Security Benchmark.
author: msmbaldwin
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6aa3e2b84c4349e2134ddeb2a60fd193f56f84e8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875978"
---
# <a name="azure-security-baseline-for-azure-web-application-firewall"></a>Azure-säkerhetsbaslinje för Azure Web Application Firewall

Den här säkerhetsbaslinjen tillämpar vägledning [från Azure Security Benchmark version 1.0](../security/benchmarks/overview-v1.md) för Azure Web Application Firewall. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure. Innehållet grupperas efter de säkerhetskontroller **som definieras** av Azure Security Benchmark och tillhörande vägledning som gäller för Azure Web Application Firewall. 

> [!NOTE]
> **Kontroller** som inte Azure Web Application Firewall eller för vilka ansvaret är Microsofts har undantagits. Information om hur Azure Web Application Firewall mappning helt till Azure Security Benchmark finns i den fullständiga **[mappningsfilen Azure Web Application Firewall säkerhetsbaslinje.](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/azure-web-application-firewall-security-baseline-v1.1.xlsx)**

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="13-protect-critical-web-applications"></a>1.3: Skydda kritiska webbprogram

**Vägledning:** Använd Microsoft Azure Web Application Firewall (WAF) för centraliserat skydd av webbprogram mot vanliga kryphål och säkerhetsproblem som SQL-ktion och skriptning mellan webbplatser. 

- Identifieringsläge: Använd det här läget för att lära dig nätverkstrafik, förstå och granska falska positiva identifieringar. Den övervakar och loggar alla hotaviseringar. Kontrollera att Diagnostik och WAF-logg är markerade och aktiverat. Observera att WAF inte blockerar inkommande begäranden när den fungerar i identifieringsläge.
- Skyddsläge: Blockerar intrång och attacker som identifieras av reglerna. En angripare får undantaget "403 obehörig åtkomst" och anslutningen stängs. Skyddsläge registrerar sådana attacker i WAF-loggarna.

Utjämna nätverkstrafiken med WAF:s identifieringsläge. När du har fastställa trafikbehoven konfigurerar du WAF i skyddsläge för att bocka oönskad trafik.

Följ upp rekommendationer med hög allvarlighetsgrad från Security Center för webbaktiverade resurser som inte skyddas av WAF.  

- [Web Application Firewall CRS-regelgrupper och regler](ag/application-gateway-crs-rulegroups-rules.md) 

- [WAF-lägen på Application Gateway](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [WAF-lägen på Front Door](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

**Vägledning:** Använd anpassade regler med Azure Web Application Firewall (WAF) för att tillåta och blockera trafik. Till exempel kan all trafik som kommer från ett intervall med IP-adresser blockeras. Konfigurera Azure WAF att köras i skyddsläge som blockerar intrång och attacker som identifieras av reglerna. En angripare får undantaget "403 obehörig åtkomst" och anslutningen stängs. Skyddsläge registrerar sådana attacker i WAF-loggarna.

- [WAF-lägen på Application Gateway](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [WAF-lägen på Front Door](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Hantera trafik till webbprogram

**Vägledning:** Azure Web Application Firewall (WAF) är kärnkomponenten i Azures skydd av webbprogram. Använd Azure WAF för att tillhandahålla ett centraliserat skydd för webbprogram mot vanliga kryphål och sårbarheter med förkonfigurerade hanterade regeluppsättning mot kända attacksignaturer från OWASP TOP 10-kategorier.

Anpassa Azure WAF med regler och regelgrupper så att de passar kraven för webbprogram och eliminera falska positiva resultat. Associera en Azure WAF-princip för varje plats bakom en WAF för att tillåta platsspecifik konfiguration. Azure WAF stöder geofiltrering, frekvensbegränsning och Azure-hanterade regler för standardregeluppsättning. och anpassade regler kan skapas för att passa behoven i ett program. 

Konfigurera Azure WAF att köras i skyddsläge efter att nätverkstrafiken har baserat sig på identifieringsläget under en bestämd tidsperiod. Azure WAF blockerar intrång och attacker som identifieras av reglerna i skyddsläge. En angripare får undantaget "403 obehörig åtkomst" och anslutningen stängs. Skyddsläge registrerar sådana attacker i WAF-loggarna.

- [WAF-lägen på Application Gateway](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [WAF-lägen på Front Door](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

- [CRS-regelgrupper och regler för brandväggen för webbaserade program](https://docs.microsoft.com/azure/web-application-firewall/ag/application-gateway-crs-rulegroups-rules?tabs=owasp31)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexitet och administrativa kostnader för nätverkssäkerhetsregler

**Vägledning:** Skapa, associera och organisera resurser logiskt i en Azure-prenumeration med taggar för identifiering av vanliga programfelkonfigurationer (till exempel Apache och IIS). 

Tillämpa regler och regelgrupper på Azure Web Application Firewall (WAF) baserat på tillämpade taggmetadata.

- [WAF-princip på Application Gateway](/cli/azure/network/application-gateway/waf-policy)

- [WAF-princip på Front Door](/cli/azure/network/front-door/waf-policy)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentera trafikkonfigurationsregler

**Vägledning:** Använd taggar för nätverkssäkerhetsgrupper som är associerade med Azure Web Application Firewall (WAF) i Azure Application Gateway-undernätet samt andra resurser som rör nätverkssäkerhet och trafikflöde. För enskilda regler för nätverkssäkerhetsgrupp använder du fältet "Beskrivning" för att ange affärs behov, varaktighet och så vidare för alla regler som tillåter trafik till eller från ett nätverk.

Använd någon av de inbyggda Azure Policy-definitioner som rör taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och för att meddela dig om befintliga otaggade resurser.

Välj Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

- [Så här skapar du en Virtual Network](../virtual-network/quick-create-portal.md)

- [Så här skapar du en NSG med en säkerhetskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

**Vägledning:** Använd Azure-aktivitetsloggen för att övervaka nätverksresurskonfigurationer och identifiera ändringar för nätverksinställningar och resurser som är relaterade till Azure Web Application Firewall-distributioner (WAF). Skapa aviseringar inom Azure Monitor som utlöses när viktiga nätverksinställningar eller resurser ändras.

- [Så här visar och hämtar du Händelser i Azure-aktivitetsloggen](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Skapa aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [Benchmark för Azure-säkerhet: Loggning och övervakning.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Använd godkända tidssynkroniseringskällor

**Vägledning:** Skapa en nätverksregel för Azure Web Application Firewall (WAF) för att tillåta åtkomst till en NTP-server med lämplig port och protokoll, till exempel port 123 över UDP.

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera central hantering av säkerhetsloggar

**Vägledning:** Konfigurera Azure Web Application Firewall (WAF) loggar som ska skickas till en central lösning för hantering av säkerhetsloggar, till exempel Azure Sentinel eller siem från tredje part. Dessa loggar omfattar Azure-aktivitetsloggar, diagnostikloggar och WAF-loggar i realtid. Dessa loggar kan sedan visas i olika verktyg, till exempel Azure Monitor, Excel och Power BI. Azure Web Application Firewall loggar ger insikter om vilka data som Azure WAF utvärderar, matchar och blockerar.

Azure Sentinel har en inbyggd Azure WAF-arbetsbok som ger en översikt över säkerhetshändelserna i Azure WAF. Den här arbetsboken innehåller händelser, matchade och blockerade regler och allt annat som loggas i brandväggsloggarna. Den här telemetrin kan användas för att starta spelboksautomatisering för att meddela eller vidta reparationsåtgärder baserat på WAF-händelser som samlas in av Azure Sentinel.

- [Visa aktivitetsloggar](../azure-resource-manager/management/view-activity-logs.md)

- [Diagnostikloggar för Application Gateway](../application-gateway/application-gateway-diagnostics.md)

- [Ansluta data från Microsofts brandvägg för webbaserade program till Azure Sentinel](../sentinel/connect-azure-waf.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägledning:** Aktivera loggning på dina Azure Web Application Firewall(WAF)-resurser för åtkomst till gransknings-, säkerhets- och diagnostikloggar. Azure Web Application Firewall innehåller detaljerad rapportering om vart och ett av de identifierade hoten som görs tillgängliga i de konfigurerade diagnostikloggarna. Aktivitetsloggar, som är automatiskt tillgängliga, inkluderar händelsekälla, datum, användare, tidsstämpel, källadresser, måladresser och andra användbara element.

- [Loggningsöversikt](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#logging)

- [Azure Monitor översikt över loggfrågor](../azure-monitor/logs/log-query-overview.md)

- [Översikt över Azure-plattformsloggar](../azure-monitor/essentials/platform-logs-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera kvarhållning av säkerhetslogglagring

**Vägledning:** Skicka Azure Web Application Firewall (WAF) till ett anpassat lagringskonto och definiera bevarandeprincipen. Använd Azure Monitor att ange kvarhållningsperioden för Log Analytics-arbetsytan baserat på organisationens efterlevnadskrav.
- [Konfigurera övervakning för ett lagringskonto](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning:** Azure Web Application Firewall (WAF) ger detaljerad rapportering om varje identifierat hot. Loggning är integrerat med Azure Diagnostics loggar som ger omfattande information om åtgärder och fel som är viktiga för granskning och felsökning. 

Azure WAF-instanser är integrerade med Security Center för att skicka aviseringar och hälsoinformation för rapportering. Använd Security Center rekommendationer för att identifiera oskyddade webbprogram och skydda dessa sårbara resurser. 

Azure Sentinel har en inbyggd brandväggsarbetsbok för brandväggshändelser som innehåller en översikt över säkerhetshändelserna på brandväggen. Dessa inkluderar händelser, matchade och blockerade regler och allt annat som loggas i brandväggsloggarna.

- [Så här aktiverar du diagnostikinställningar för Azure-aktivitetsloggen](/azure/azure-monitor/platform/activity-log)

- [Så här aktiverar du diagnostikinställningar för Azure Application Gateway](../application-gateway/application-gateway-diagnostics.md)

- [Övervaka mått och loggar i Azure Front Door](../frontdoor/front-door-diagnostics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivera aviseringar för avvikande aktiviteter

**Vägledning:** Aktivera diagnostikinställningar för Azure-aktivitetsloggen, samt diagnostikinställningar för din Azure WAF och skicka loggarna till en Log Analytics-arbetsyta. Utför frågor i Log Analytics för att söka efter termer, identifiera trender, analysera mönster och ge många andra insikter baserat på insamlade data. Skapa aviseringar för avvikande aktivitet baserat på WAF-mått. Om till exempel blockerat antal begäranden överskrider "X" gör du "Y".

- [Så här aktiverar du diagnostikinställningar för Azure-aktivitetsloggen](/azure/azure-monitor/essentials/diagnostic-settings-legacy)

- [Så här skapar du aviseringar i Azure](../azure-monitor/alerts/tutorial-response.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralisera loggning mot skadlig kod

**Vägledning:** Distribuera Azure Web Application Firewall (WAF) framför kritiska webbprogram för ytterligare kontroll av inkommande trafik. 

Azure WAF ger ett centraliserat skydd av dina webbprogram mot vanliga kryphål och sårbarheter och skyddar dina appar genom att inspektera inkommande webbtrafik för att blockera attacker som SQL- injections, skriptangrepp mellan webbplatser, uppladdning av skadlig kod och DDoS-attacker.

- [Så här distribuerar du Azure WAF](ag/create-waf-policy-ag.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Upprätthålla en förteckning över administrativa konton

**Vägledning:** Azure Active Directory (Azure AD) har inbyggda roller som kan frågas och måste tilldelas explicit. Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

- [Så här får du en katalogroll i Azure AD med PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Så här hämtar du medlemmar i en katalogroll i Azure AD med PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Använd dedikerade administrativa konton

**Vägledning:** Det finns inga lokala administratörstilldelningar tillgängliga i WAF. Det kan dock finnas Azure Active Directory (Azure AD) administratörsroller i miljön som kan ge hanteringskontroll över Azure WAF-resurser.
Det är en bra idé att skapa standardprocedurer för användning av dedikerade administrativa konton som har åtkomst till Azure Web Application Firewall instanser (WAF). Använd Security Center funktioner för identitets- och åtkomsthantering för att övervaka antalet administrativa konton.

- [Förstå Azure Security Center identitet och åtkomst](../security-center/security-center-identity-access.md)

- [Förstå hur du skapar administratörsanvändare i Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/howto-create-users#the-server-admin-account)

- [Så här använder du Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använd multifaktorautentisering för Azure Active Directory åtkomstbaserad åtkomst

**Vägledning:** Aktivera Azure Active Directory (Azure AD) multifaktorautentisering och följ Security Center rekommendationer för identitets- och åtkomsthantering.

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst inom Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Använd dedikerade datorer (arbetsstationer för privilegierad åtkomst) för alla administrativa uppgifter

**Vägledning:** Använd PAW (Privileged Access Workstation) med multifaktorautentisering som konfigurerats för att logga in på och konfigurera Azure Web Application Firewall (WAF) och relaterade resurser.

- [Läs mer om arbetsstationer med privilegierad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logga och varna om misstänkta aktiviteter från administrativa konton

**Vägledning:** Använd Azure Active Directory (Azure AD) för generering av loggar och aviseringar när misstänkt eller osäker aktivitet inträffar i miljön. Använd Security Center för att övervaka identitets- och åtkomstaktivitet.

- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här övervakar du användarnas identitets- och åtkomstrelaterade aktiviteter i Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Hantera Azure-resurser från endast godkända platser

**Vägledning:** Konfigurera platsvillkoret för en princip för villkorsstyrd åtkomst och hantera dina namngivna platser.

Skapa logiska grupperingar av IP-adressintervall eller länder och regioner med namngivna platser. Begränsa åtkomsten till känsliga resurser, till exempel Azure Key Vault, till dina konfigurerade namngivna platser.

- [Vad är platsvillkoret för villkorlig Azure Active Directory (Azure AD)](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning:** Använd Azure Active Directory (Azure AD) som centralt system för autentisering och auktorisering. Azure AD skyddar data med hjälp av stark kryptering för data i vila och under överföring och även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.
- [Skapa och konfigurera en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Granska och stämma av användaråtkomst regelbundet

**Vägledning:** Azure Active Directory (Azure AD) innehåller loggar som hjälper dig att identifiera inaktuella konton. Använd Azure Identity Access Reviews för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Granska användaråtkomst regelbundet för att se till att endast aktiva användare har fortsatt åtkomst.

- [Förstå Azure AD-rapportering](/azure/active-directory/reports-monitoring)

- [Så här använder du Azure Identity Access Reviews](../active-directory/governance/access-reviews-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning:** Integrera Azure Active Directory(Azure AD) inloggningsaktivitet, källor för gransknings- och riskhändelseloggar med val annat SIEM- eller övervakningsverktyg som Azure Sentinel.

Effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka granskningsloggar och inloggningsloggar till en Log Analytics-arbetsyta. Konfigurera önskade aviseringar i Log Analytics-arbetsytan.

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Avisering om kontobeteendeavvikelse vid inloggning

**Vägledning:** Använd Azure Active Directory (Azure AD) risk- och identitetsskyddsfunktioner (Azure AD) för att konfigurera automatiserade svar på identifierade misstänkta åtgärder relaterade till användaridentiteter. Mata in data i Azure Sentinel för vidare undersökning.

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du riskprinciper för Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Upprätthålla en förteckning över känslig information

**Vägledning:** Använd taggar för att spåra Azure Web Application Firewall (WAF) och relaterade resurser som lagrar eller bearbetar känslig information.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolera system som lagrar eller bearbetar känslig information

**Vägledning:** Implementera isolering med separata prenumerationer och hanteringsgrupper för enskilda säkerhetsdomäner, till exempel miljötyp och datakänslighetsnivå, till exempel utvecklings-, test- och produktionsmiljöer. 

Kontrollera åtkomsten till Azure-Azure Active Directory med rollbaserad åtkomstkontroll (Azure AD) (Azure RBAC).

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Kryptera all känslig information under överföring

**Vägledning:** Kryptera all känslig information under överföring. Se till att alla klienter som ansluter till dina Azure Web Application Firewall-instanser (WAF) och relaterade resurser kan förhandla TLS 1.2 eller högre.

Följ Security Center rekommendationer för kryptering i vila och kryptering under överföring där det är tillämpligt.

- [Förstå kryptering under överföring med Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Använd Azure RBAC för att hantera åtkomst till resurser 

**Vägledning:** Kontrollera åtkomsten till dina Azure-resurser som Web Application Firewall med rollbaserad åtkomstkontroll i Azure (Azure RBAC).

- [Så här konfigurerar du Azure RBAC i Azure](../role-based-access-control/role-assignments-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Kryptera känslig information i vila

**Vägledning:** Använd kryptering i vila för alla Azure-resurser, Azure Web Application Firewall (WAF) och relaterade resurser. Microsoft rekommenderar att du låter Azure hantera dina krypteringsnycklar, men det finns ett alternativ för att hantera dina egna nycklar i vissa instanser.

- [Förstå kryptering vid vila i Azure](../security/fundamentals/encryption-atrest.md)

- [Så här konfigurerar du kund hanterade krypteringsnycklar](https://docs.microsoft.com/azure/storage/common/customer-managed-keys-configure-key-vault?tabs=portal)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisering om ändringar av viktiga Azure-resurser

**Vägledning:** Konfigurera Azure Web Application Firewall (WAF) så att den körs i skyddsläge efter att nätverkstrafiken har baserat sig på identifieringsläget under en förbestämd tid. 

Azure WAF i skyddsläge blockerar intrång och attacker som identifieras av reglerna. Angriparen får undantaget "403 obehörig åtkomst" och anslutningen stängs. Skyddsläge registrerar sådana attacker i WAF-loggarna.

- [Översikt över integrering mellan Application Gateway och Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-partner-integration#overview)

- [WAF-lägen på Application Gateway](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [WAF-lägen på Front Door](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security Benchmark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Använd automatisk tillgångsidentifiering

**Vägledning:** Använd Azure Resource Graph för att fråga efter eller identifiera alla resurser, till exempel beräkning, lagring, nätverk, portar och protokoll och så vidare i dina prenumerationer.

Se till att du har rätt (läs) behörigheter i din klientorganisation och räkna upp alla Azure-prenumerationer samt resurserna i dina prenumerationer. Även om klassiska Azure-resurser kan Resource Graph via en Resource Graph rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser i framtiden.

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="62-maintain-asset-metadata"></a>6.2: Underhålla tillgångsmetadata

**Vägledning:** Använda taggar för Azure Web Application Firewall (WAF). Taggar kan associeras med resurser och tillämpas logiskt för att organisera åtkomsten till dessa resurser i en kundprenumeration. 

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ta bort obehöriga Azure-resurser

**Vägledning:** Använd taggning, hanteringsgrupper och separata prenumerationer när det är lämpligt för att organisera och spåra Azure WAF och relaterade resurser. Stämma av inventeringen regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definiera och underhålla inventering av godkända Azure-resurser

**Vägledning:** Skapa en förteckning över godkända resurser, inklusive konfiguration baserat på organisationens behov.

Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i dina prenumerationer. Använd Azure Resource Graph till att fråga efter och identifiera resurser i prenumerationerna. Se till att alla Azure-resurser som finns i miljön godkänns.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Övervaka för ej godkända Azure-resurser

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i dina prenumerationer.
Använd Azure Resource Graph att fråga efter eller identifiera Azure Web Application Firewall (WAF) resurser i sina prenumerationer. Se till att alla Azure WAF och relaterade resurser som finns i miljön godkänns.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Ta bort ej godkända Azure-resurser och program

**Vägledning:** Övervaka och ta bort ej godkända Azure WAF-resurser med Azure Policy för att neka distributionen av Azure WAF, eller en viss typ av WAF, till exempel Azure WAF v1 eller V2.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="69-use-only-approved-azure-services"></a>6.9: Använd endast godkända Azure-tjänster

**Vägledning:** Använd Azure Policy för att begränsa vilka tjänster du kan etablera i din miljö.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en viss resurstyp med Azure Policy](/azure/governance/policy/samples/built-in-policies#general)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning:** Använd villkorsstyrd åtkomst i Azure för att begränsa en användares möjlighet att interagera med Azure Resources Manager genom att konfigurera "Blockera åtkomst" för appen "Microsoft Azure Management".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resources Manager](../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fysiskt eller logiskt avsegrera program med hög risk

**Vägledning:** Azure Web Application Firewall (WAF) kan associeras med olika miljöer via nätverk, resursgrupper eller prenumerationer för att åtsegrera program med hög risk.

- [Översikt över Azure Virtual Network](../virtual-network/virtual-networks-overview.md)

- [Ordna resurser med hanteringsgrupper i Azure](../governance/management-groups/overview.md)

- [Beslutsguide för prenumerationer](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security Benchmark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning:** Definiera och implementera standardsäkerhetskonfigurationer för nätverksinställningar som är relaterade Azure Web Application Firewall distributioner (WAF).

Använd Azure Policy alias i namnområdet "Microsoft.Network" för att skapa anpassade principer för att granska eller framtvinga nätverkskonfigurationen för dina Azure Application-gatewayer, virtuella nätverk, nätverkssäkerhetsgrupper och använda inbyggda principdefinitioner.

- [Visa tillgängliga Azure Policy alias](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Underhålla säkra Azure-resurskonfigurationer

**Vägledning:** Använd Azure Policy [neka] och [distribuera om det inte finns] effekter för att framtvinga säkra inställningar för dina Azure Web Application Firewall (WAF) och relaterade resurser. 

Använd Azure Resource Manager för att upprätthålla säkerhetskonfigurationen för din Azure WAF och relaterade resurser som krävs av din organisation.

- [Förstå Azure Policy effekter](../governance/policy/concepts/effects.md)

- [Skapa och hantera principer för att använda kompatibilitet](../governance/policy/tutorials/create-and-manage.md)

- [översikt Azure Resource Manager mallar](../azure-resource-manager/templates/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Lagra konfiguration av Azure-resurser på ett säkert sätt

**Vägledning:** Använda Azure DevOps för att lagra och hantera din kod på ett säkert sätt, till exempel anpassade Azure-principer och Azure Resource Manager mallar.

Bevilja eller neka behörigheter till specifika användare, inbyggda säkerhetsgrupper eller grupper som definierats i Azure Active Directory (Azure AD), om de är integrerade med Azure DevOps eller Azure AD, om de är integrerade med Team Foundation Server (TFS).

- [Så här lagrar du kod i Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Om behörigheter och grupper i Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuera konfigurationshanteringsverktyg för Azure-resurser

**Vägledning:** Använd inbyggda Azure Policy-definitioner samt Azure Policy-alias i namnområdet "Microsoft.Network" för att skapa anpassade principer för avisering, granskning och framtvinga systemkonfigurationer. Utveckla en process och pipeline för hantering av principundantag.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Dokumentation om Azure Policy](/azure/governance/policy)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementera automatisk konfigurationsövervakning för Azure-resurser

**Vägledning:** Använd inbyggda Azure Policy-definitioner samt Azure Policy-alias i namnområdet "Microsoft.Network" för att skapa anpassade principer för avisering, granskning och framtvinga systemkonfigurationer. 

Använd Azure Policy [audit], [deny] och [distribuera om det inte finns] för att automatiskt framtvinga konfigurationer för dina Azure-resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Dokumentation om Azure Policy](/azure/governance/policy)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning:** Använd Azure Key Vault att lagra certifikat på ett säkert sätt. Azure Key Vault är ett plattforms hanterat hemlighetslager som du kan använda för att skydda hemligheter, nycklar och SSL-certifikat. 

Azure Application Gateway stöder integrering med Key Vault för servercertifikat som är kopplade till HTTPS-aktiverade lyssnare. 

- [Så här konfigurerar du SSL-avslutning Key Vault certifikat med hjälp av Azure PowerShell](../application-gateway/configure-keyvault-ps.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminera oavsiktlig exponering av autentiseringsuppgifter

**Vägledning:** Implementera skanner för autentiseringsuppgifter för att identifiera autentiseringsuppgifter i koden, vilket även uppmuntrar till flytt av identifierade autentiseringsuppgifter till säkrare platser som Azure Key Vault.
- [Konfigurera skanner för autentiseringsuppgifter](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Prestandatest för Azure-säkerhet: Dataåterställning.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Skydda säkerhetskopior och kund hanterade nycklar

**Vägledning:** Se till att mjuk borttagning är aktiverat för Azure Key Vault. Mjuk borttagning tillåter återställning av borttagna nyckelvalv och valvobjekt som nycklar, hemligheter och certifikat.

- [Så här använder Azure Key Vault mjuk borttagning](https://docs.microsoft.com/azure/key-vault/general/key-vault-recovery?tabs=azure-powershell&amp;preserve-view=true)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Skapa en guide för incidentsvar

**Vägledning:** Utveckla en guide för incidenter för din organisation. Se till att det finns skriftliga planer för incidenthantering som definierar alla roller för personal samt faserna för incidenthantering och incidenthantering från identifiering till granskning efter incident. 

- [Vägledning om hur du skapar en egen process för svar på säkerhetsincidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft Security Response Center är en incidents uppbyggnad](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Använd NIST:s guide för hantering av säkerhetsincidenter för att hjälpa dig att skapa en egen plan för incidenthantering](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Skapa en procedur för incidentbedömning och prioritering

**Vägledning:** Security Center tilldelar en allvarlighetsgrad till varje avisering för att prioritera vilka aviseringar som ska undersökas först. Allvarlighetsgraden baseras på hur säker Security Center är i hitta eller det mått som används för att utfärda aviseringen samt konfidensnivån för att det fanns skadlig avsikt bakom aktiviteten som ledde till aviseringen.

Markera prenumerationer tydligt (till exempel produktion, icke-produktion) och skapa ett namngivningssystem för att tydligt identifiera och kategorisera Azure-resurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="103-test-security-response-procedures"></a>10.3: Testa procedurer för säkerhetssvar

**Vägledning:** Genomför övningar för att regelbundet testa systemets incidentsvarsfunktioner. Identifiera svaga punkter och luckor, och ändra planen efter behov.
- [Se NIST:s publikation Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Guide till test-, tränings- och övningsprogram för IT-planer och IT-funktioner)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Ange kontaktinformation om säkerhetsincidenter och konfigurera aviseringsmeddelanden för säkerhetsincidenter

**Vägledning:** Kontaktinformation om säkerhetsincident används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har använts av en obehörig eller obehörig part. Granska incidenter efter fakta för att se till att problemen är lösta.
- [Så här ställer du Azure Security Center säkerhetskontakt](../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Införliva säkerhetsaviseringar i systemet för incidentsvar

**Vägledning:** Exportera dina Security Center och rekommendationer med hjälp av funktionen Kontinuerlig export. Med löpande export kan du exportera aviseringar och rekommendationer manuellt eller kontinuerligt. Använd Azure Security Center dataanslutningsappen för att strömma aviseringarna till Azure Sentinel enligt organisationens krav.

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisera svaret på säkerhetsaviseringar

**Vägledning:** Använd funktionen Arbetsflödesautomation i Security Center för att automatiskt utlösa svar via "Logic Apps" för säkerhetsaviseringar och rekommendationer.
- [Så här konfigurerar du arbetsflödesautomation och Logic Apps](../security-center/workflow-automation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [Azure Security Benchmark: Penetration Tests (Intrångstester) och Red Team Exercises (Red Team-övningar).](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Genomför regelbundna intrångstester av dina Azure-resurser och se till att alla viktiga säkerhetsresultat åtgärdas

**Vägledning:** Följ Microsofts regler för engagemang för att säkerställa att intrångstester inte bryter mot Microsofts principer. Använd Microsofts strategi och körning av Red Teaming och intrångstestning på live-webbplatser mot Microsoft-hanterad molninfrastruktur, -tjänster och -program. 

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
