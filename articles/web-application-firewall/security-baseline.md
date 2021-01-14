---
title: Azures säkerhets bas linje för Azure Web Application-brandvägg
description: Säkerhets bas linjen för Azure Web Application-brandväggen ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b44ea6f55717712cfba4045205e02bd9f2b851df
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202665"
---
# <a name="azure-security-baseline-for-azure-web-application-firewall"></a>Azures säkerhets bas linje för Azure Web Application-brandvägg

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 1,0](../security/benchmarks/overview-v1.md) till Azure WebApplication-brandvägg. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure. Innehållet grupperas efter de **säkerhets kontroller** som definierats av Azures säkerhets benchmark och relaterade rikt linjer som gäller för brand vägg för Azure-webbprogram. **Kontroller** som inte kan användas i brand väggen för Azure Web Application har uteslutits. 

Om du vill se hur Azure-programbrand väggen fullständigt mappar till Azures säkerhets benchmark kan du läsa mer i den [fullständiga Azure-webbprogrammet brand vägg för säkerhets bas linje mappning](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets principen för Azure-säkerhet: nätverks säkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: Använd Microsoft Azure brand vägg för webbaserade program (WAF) för centraliserat skydd av webb program från vanliga sårbarheter och sårbarheter som SQL-inmatning och Cross-Site-skript. 

- Identifierings läge: Använd det här läget för att lära dig om nätverks trafiken, förstå och granska falska positiva identifieringar. Den övervakar och loggar alla hot aviseringar. Kontrol lera att diagnostik-och WAF-loggen är markerade och aktiverade. Observera att WAF inte blockerar inkommande begär anden när den körs i identifierings läge.
- Skydds läge: blockerar intrång och attacker som upptäckts av reglerna. En angripare får ett "403 obehörig åtkomst"-undantag och anslutningen stängs. I skydds läget registreras sådana attacker i WAF-loggarna.

Bas linje din nätverks trafik med WAF identifierings läge. När du har fastställt dina trafik behov konfigurerar du WAF i skydds läge till bock-oönskad trafik.

Följ upp rekommendationer för hög allvarlighets grad från Security Center för alla webb-aktiverade resurser som inte skyddas av WAF.  

- [Regel grupper och regler för brand vägg för webb program brand vägg](ag/application-gateway-crs-rulegroups-rules.md) 

- [WAF-lägen på Application Gateway](ag/ag-overview.md#waf-modes)

- [WAF lägen på front dörren](afds/afds-overview.md#waf-modes)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Använd anpassade regler med Azure Web Application-BRANDVÄGGEN (WAF) för att tillåta och blockera trafik. Till exempel kan all trafik som kommer från ett intervall av IP-adresser blockeras. Konfigurera Azure-WAF för att köras i skydds läge, vilket blockerar intrång och attacker som upptäckts av reglerna. En angripare får ett "403 obehörig åtkomst"-undantag och anslutningen stängs. I skydds läget registreras sådana attacker i WAF-loggarna.

- [WAF-lägen på Application Gateway](ag/ag-overview.md#waf-modes)

- [WAF lägen på front dörren](afds/afds-overview.md#waf-modes)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: Azure Web Application FIREWALL (WAF) är kärn komponenten i Azures webb program skydd. Använd Azure-WAF för att tillhandahålla centraliserat skydd för webb program från vanliga sårbarheter och sårbarheter med förkonfigurerade hanterade ruleset mot kända attack-signaturer från OWASP topp 10-kategorier.

Anpassa Azure-WAF med regler och regel grupper för att passa webb program krav och eliminera falska positiva identifieringar. Koppla en Azure WAF-princip för varje plats bakom en WAF för att tillåta platsspecifika konfiguration. Azure WAF stöder geo-filtrering, hastighets begränsning, Azure-hanterade standard regel uppsättnings regler. Du kan skapa anpassade regler som passar programmens behov. 

Konfigurera Azure-WAF att köras i förebyggande läge när bas linje nätverks trafiken i identifierings läge under en bestämd tids period. Azure-WAF blockerar intrång och attacker som upptäckts av reglerna i skydds läge. En angripare får ett "403 obehörig åtkomst"-undantag och anslutningen stängs. I skydds läget registreras sådana attacker i WAF-loggarna.

- [WAF-lägen på Application Gateway](ag/ag-overview.md#waf-modes)

- [WAF lägen på front dörren](afds/afds-overview.md#waf-modes)

- [Regel grupper och regler för brand vägg för webb program brand vägg](ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: skapa, associera och logiskt organisera resurser i en Azure-prenumeration med taggar för identifiering av vanliga program konfigurationer (t. ex. Apache och IIS). 

Tillämpa regler och regel grupper i WAF-principer (Azure Web Application Firewall) baserat på de metadata som används för taggar.

- [WAF princip på Application Gateway](/cli/azure/network/application-gateway/waf-policy?view=azure-cli-latest) 

- [WAF-princip på front dörren](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för nätverks säkerhets grupper som är kopplade till Azure Web Application-BRANDVÄGGEN (WAF) i ditt Azure Application Gateway-undernät samt andra resurser som rör nätverks säkerhets-och trafikflödet. Använd fältet Beskrivning för enskilda regler för nätverks säkerhets grupper för att ange affärs behov, varaktighet och så vidare, för alla regler som tillåter trafik till eller från ett nätverk.

Använd någon av de inbyggda Azure Policy definitionerna som är relaterade till taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och meddela dig om befintliga otaggade resurser.

Välj Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

- [Så här skapar du en Virtual Network](../virtual-network/quick-create-portal.md)

- [Så här skapar du en NSG med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar för nätverks inställningar och resurser som är relaterade till dina Azure Web Application FIREWALL (WAF)-distributioner. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks inställningar eller resurser sker.

- [Visa och hämta Azure aktivitets logg händelser](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets benchmark för Azure: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

**Vägledning**: skapa en nätverks regel för Azure brand vägg för webbaserade program (WAF) för att tillåta åtkomst till en NTP-server med lämplig port och protokoll, till exempel port 123 över UDP.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: Konfigurera Azure Web Application FIREWALL (WAF)-loggar som ska skickas till en central lösning för säkerhets logg hantering, till exempel Azure SENTINEL eller Siem från tredje part. I dessa loggar ingår Azure Activity, Diagnostic och WAF-loggar i real tid. dessa loggar kan sedan visas i olika verktyg, till exempel Azure Monitor, Excel och Power BI. Azure Web Application Firewall-loggar ger insikt om vilka data som Azure-WAF utvärderar, matchar och blockerar.

Azure Sentinel har en inbyggd Azure WAF-arbetsbok som ger en översikt över säkerhets händelser på Azure-WAF. Den här arbets boken innehåller händelser, matchade och blockerade regler och allt annat som loggas i brand Väggs loggarna. Den här Telemetrin kan användas för att starta Spelbok Automation för att meddela eller vidta åtgärder baserat på WAF-händelser som samlas in av Sentinel.

- [Visa aktivitets loggar](../azure-resource-manager/management/view-activity-logs.md)

- [Diagnostikloggar för Application Gateway](../application-gateway/application-gateway-diagnostics.md)

- [Anslut data från Microsoft Web Application-brandväggen till Azure Sentinel](../sentinel/connect-azure-waf.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Aktivera loggning i WAF-resurser (Azure Web Application Firewall) för åtkomst till gransknings-, säkerhets-och diagnostikloggar. Azure Web Application-brandväggen ger detaljerad rapportering om var och en av de identifierade hoten som görs tillgängliga i de konfigurerade diagnostikloggar. Aktivitets loggar, som är automatiskt tillgängliga, innehåller händelse källa, datum, användare, tidsstämpel, käll adresser, mål adresser och andra användbara element.

- [Loggnings översikt](ag/ag-overview.md#logging)

- [Översikt över Azure Monitor logg fråga](../azure-monitor/log-query/log-query-overview.md)

- [Översikt över Azures plattforms loggar](../azure-monitor/platform/platform-logs-overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: skicka WAF-loggarna (Azure Web Application Firewall) till ett anpassat lagrings konto och definiera bevarande principen. Använd Azure Monitor för att ställa in din Log Analytics kvarhållningsperiod för arbets ytor baserat på organisationens krav på efterlevnad.
- [Konfigurera övervakning för ett lagrings konto](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: Azure brand vägg för webbaserade program (WAF) innehåller detaljerad rapportering om varje identifierat hot. Loggning är integrerat med Azure-diagnostik loggar som ger omfattande information om åtgärder och fel som är viktiga för granskning och fel sökning. 

Azure WAF-instanser är integrerade med Security Center för att skicka aviseringar och hälso information för rapportering. Använd Security Center rekommendationer för att identifiera oskyddade webb program och skydda dessa sårbara resurser. 

Azure Sentinel har en inbyggd arbets WAF för brand Väggs händelser, som ger en översikt över säkerhets händelserna på WAF. Detta omfattar händelser, matchade och blockerade regler och allt annat som loggas i brand Väggs loggarna.

- [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](../azure-monitor/index.yml) 

- [Så här aktiverar du diagnostikinställningar för Azure Application Gateway](../application-gateway/application-gateway-diagnostics.md)

- [Övervaka mått och loggar i Azures front dörr](../frontdoor/front-door-diagnostics.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: Aktivera diagnostikinställningar för Azure aktivitets logg, samt diagnostikinställningar för Azure-WAF, och skicka loggarna till en Log Analytics-arbetsyta. Utför frågor i Log Analytics för att söka efter termer, identifiera trender, analysera mönster och tillhandahålla många andra insikter baserat på insamlade data. Skapa aviseringar för avvikande aktivitet baserat på WAF mått. Till exempel, om blockerat antal begär Anden som överstiger "X", "Y".

- [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](../azure-monitor/platform/activity-log.md)

- [Så här skapar du aviseringar i Azure](../azure-monitor/learn/tutorial-response.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: Distribuera Azure Web Application FIREWALL (WAF) framför viktiga webb program för ytterligare inspektion av inkommande trafik. 

Azure WAF tillhandahåller centraliserat skydd av dina webb program från vanliga sårbarheter och sårbarheter och skyddar dina appar genom att inspektera inkommande webb trafik för att blockera attacker som SQL-injektering, skript körning över flera webbplatser, överföringar av skadlig kod och DDoS-attacker.

- [Så här distribuerar du Azure-WAF](ag/create-waf-policy-ag.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azures säkerhets benchmark: identitets-och åtkomst kontroll](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: Azure Active Directory (Azure AD) har inbyggda roller är frågekörning och måste tilldelas explicit. Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

- [Så här hämtar du en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: inga lokala administratörs tilldelningar är tillgängliga i WAF. Det kan dock finnas Azure Active Directory (Azure AD)-administratörs roller i miljön som kan tillåta hanterings kontroll över Azure WAF-resurser.
Det är en bra idé att skapa standard procedurer kring användningen av dedikerade administrativa konton som har åtkomst till Azure Web Application Firewall (WAF)-instanser. Använd Security Center identitets-och åtkomst hanterings funktioner för att övervaka antalet administrativa konton.

- [Förstå Azure Security Center identitet och åtkomst](../security-center/security-center-identity-access.md)

- [Lär dig hur du skapar administratörs användare i Azure Database for PostgreSQL](../postgresql/howto-create-users.md#the-server-admin-account)

- [Använda Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory-baserad åtkomst

**Vägledning**: Aktivera Azure Active Directory (Azure AD) Multi-Factor Authentication (MFA) och följ Security Centers rekommendationer för identitets-och åtkomst hantering.

- [Aktivera MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd Privileged Access Workstation (Paw) med Multi-Factor Authentication (MFA) som kon figurer ATS för att logga in på och konfigurera Azure Web Application FIREWALL (WAF) och relaterade resurser. 

- [Lär dig mer om arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Aktivera MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: använda Azure Active Directory (Azure AD) säkerhets rapporter för generering av loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön. Använd Security Center för att övervaka identitets-och åtkomst aktiviteter.

- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här övervakar du användarnas identitets- och åtkomstrelaterade aktiviteter i Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Konfigurera plats villkoret för en princip för villkorlig åtkomst och hantera dina namngivna platser. 

Skapa logiska grupperingar av IP-adressintervall eller länder och regioner med namngivna platser. Begränsa åtkomsten till känsliga resurser, till exempel Azure Key Vault hemligheter, till dina konfigurerade namngivna platser.

- [Vad är plats villkoret i Azure Active Directory villkorlig åtkomst](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: använda Azure Active Directory (Azure AD) som central autentiserings-och auktoriserings system. Azure AD skyddar data med stark kryptering för data i vila och under överföring och även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.
- [Skapa och konfigurera en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure Active Directory (Azure AD) innehåller loggar som hjälper till att identifiera inaktuella konton. Använd granskningar av Azure Identity Access för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Granska användar åtkomst regelbundet så att endast aktiva användare har fortsatt åtkomst.

- [Förstå Azure AD repor ting](../active-directory/reports-monitoring/index.yml)

- [Så här använder du granskningar av Azure Identity Access](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: integrera Azure Active Directory (Azure AD) inloggnings aktivitet, gransknings-och risk händelse logg källor med alla Siem-eller övervaknings verktyg som Azure Sentinel.

Effektivisera den här processen genom att skapa diagnostikinställningar för Azure Active Directory (Azure AD)-användar konton och skicka gransknings loggarna och inloggnings loggarna till en Log Analytics arbets yta. Konfigurera önskade aviseringar inom arbets ytan Log Analytics.

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: varning vid inloggnings beteende för konto

**Vägledning**: Använd Azure Active Directorys (Azure AD)-risk-och identitets skydds funktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter. Mata in data i Azure Sentinel för ytterligare undersökning.

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security benchmark: Data Protection](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att under lätta spårning av Azure Web Application FIREWALL (WAF) och relaterade resurser som lagrar eller bearbetar känslig information.
- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera isolering med separata prenumerationer och hanterings grupper för enskilda säkerhets domäner som miljö typ och data känslighets nivå, till exempel utvecklings-, test-och produktions miljöer. 

Kontrol lera åtkomsten till Azure-resurser med rollbaserad åtkomst kontroll i Azure (Azure RBAC).

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: kryptera all känslig information under överföring. Se till att alla klienter som ansluter till dina Azure Web Application Firewall (WAF)-instanser och relaterade resurser kan förhandla TLS 1,2 eller senare.

Följ Security Center rekommendationer för kryptering i vila och kryptering under överföring i förekommande fall.

- [Förstå kryptering i överföring med Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser

**Vägledning**: kontrol lera åtkomsten till Azure-resurser med rollbaserad åtkomst kontroll i Azure (Azure RBAC).
- [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: Använd kryptering i vila för alla Azure-resurser, inklusive Azure Web Application FIREWALL (WAF) och relaterade resurser. Microsoft rekommenderar att Azure hanterar dina krypterings nycklar, men det finns möjlighet att hantera dina egna nycklar i vissa instanser.

- [Förstå kryptering vid vila i Azure](../security/fundamentals/encryption-atrest.md)

- [Konfigurera kundens hanterade krypterings nycklar](../storage/common/customer-managed-keys-configure-key-vault.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Konfigurera Azure Web Application FIREWALL (WAF) för att köras i skydds läge när bas linje nätverks trafiken i identifierings läge för en förutbestämd tids period. 

Azure-WAF, i förebyggande läge, blockerar intrång och attacker som identifieras av reglerna. Angriparen får ett undantag för "403 obehörig åtkomst" och anslutningen stängs. I skydds läget registreras sådana attacker i WAF-loggarna.

- [Översikt över integrering mellan Application Gateway och Azure Security Center](../application-gateway/application-gateway-integration-security-center.md#overview)

- [WAF-lägen på Application Gateway](ag/ag-overview.md#waf-modes)

- [WAF lägen på front dörren](afds/afds-overview.md#waf-modes)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga eller identifiera alla resurser, till exempel data bearbetning, lagring, nätverk, portar och protokoll, och så vidare i dina prenumerationer. 

Se till att du har rätt (Läs) behörigheter i din klient organisation och räkna upp alla Azure-prenumerationer samt resurserna i dina prenumerationer. Även om klassiska Azure-resurser kan identifieras via resurs diagram, rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser som går framåt.

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: använda taggar i Azure Web Application FIREWALL (WAF)-principer. Taggar kan associeras med resurser och tillämpas logiskt för att organisera åtkomst till dessa resurser i en kund prenumeration. 

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer, vid behov, för att organisera och spåra Azure-WAF och relaterade resurser. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla inventering av godkända Azure-resurser

**Vägledning**: skapa en inventering av godkända resurser inklusive konfiguration utifrån organisationens behov.

Använd Azure Policy för att ange begränsningar för den typ av resurser som kan skapas i dina prenumerationer. Använd Azure Resource Graph till att fråga efter och identifiera resurser i prenumerationerna. Se till att alla Azure-resurser som finns i miljön är godkända.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i dina prenumerationer.
Använd Azure Resource Graph för att fråga eller identifiera WAF-resurser (Azure Web Application Firewall) i prenumerationerna. Se till att alla Azure-WAF och relaterade resurser som finns i miljön godkänns.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: övervaka och ta bort icke godkända Azure WAF-resurser med Azure policy för att neka distributionen av Azure WAF eller en viss typ av WAF, till exempel Azure WAF v1 vs v2.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att begränsa vilka tjänster du kan etablera i din miljö.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/index.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd villkorlig åtkomst i Azure för att begränsa en användares möjlighet att interagera med Azures resurs hanterare genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azures resurs hanterare](../role-based-access-control/conditional-access-azure-management.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: Azure brand vägg för webbaserade program (WAF) kan kopplas till olika miljöer via nätverk, resurs grupper eller prenumerationer för att åtskilja program med hög risk.

- [Översikt över Azure Virtual Network](../virtual-network/virtual-networks-overview.md)

- [Ordna resurser med hanteringsgrupper i Azure](../governance/management-groups/overview.md)

- [Beslutsguide för prenumerationer](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security benchmark: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för nätverks inställningar som är relaterade till dina WAF-distributioner (Azure Web Application Firewall).
Använd Azure Policy alias i namn området "Microsoft. Network" om du vill skapa anpassade principer för att granska eller tillämpa nätverks konfigurationen för dina Azure Application gateways, virtuella nätverk, nätverks säkerhets grupper och använda inbyggda princip definitioner.

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns]-effekter för att genomdriva säkra inställningar i Azure Web Application FIREWALL (WAF) och relaterade resurser. 

Använd Azure Resource Manager mallar för att underhålla säkerhets konfigurationen för dina Azure-WAF och relaterade resurser som krävs av din organisation.

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

- [Skapa och hantera principer för att använda kompatibilitet](../governance/policy/tutorials/create-and-manage.md)

- [Översikt över Azure Resource Manager mallar](../azure-resource-manager/templates/overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: Använd Azure-DevOps för att lagra och hantera din kod på ett säkert sätt, t. ex. anpassade Azure-principer och Azure Resource Manager mallar. 

Bevilja eller neka behörigheter till vissa användare, inbyggda säkerhets grupper eller grupper som definierats i Azure Active Directory (Azure AD), om de är integrerade med Azure DevOps eller Active Directory, om det är integrerat med Team Foundation Server (TFS).

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Om behörigheter och grupper i Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: använd inbyggda Azure policy definitioner samt Azure policy alias i namn området "Microsoft. Network" för att skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Utveckla en process och pipeline för att hantera princip undantag.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Dokumentation om Azure Policy](../governance/policy/index.yml)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: använd inbyggda Azure policy definitioner samt Azure policy alias i namn området "Microsoft. Network" för att skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. 

Använd Azure Policy [granskning], [neka] och [distribuera om det inte finns]-effekter för att automatiskt tillämpa konfigurationer för dina Azure-resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Dokumentation om Azure Policy](../governance/policy/index.yml)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Använd Azure Key Vault för att lagra certifikat på ett säkert sätt. Azure Key Vault är ett plattforms hanterat hemligt arkiv som du kan använda för att skydda hemligheter, nycklar och SSL-certifikat. 

Azure Application Gateway stöder integrering med Key Vault för Server certifikat som är anslutna till HTTPS-aktiverade lyssnare. 

- [Så här konfigurerar du SSL-avslutning med Key Vault certifikat med hjälp av Azure PowerShell](../application-gateway/configure-keyvault-ps.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifter för inloggning för att identifiera autentiseringsuppgifter inom kod som också uppmuntrar till att flytta identifierade autentiseringsuppgifter till säkrare platser som Azure Key Vault.
- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Azure Security benchmark: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: se till att mjuk borttagning är aktiverat för Azure Key Vault. Med mjuk borttagning kan du återställa borttagna nyckel valv och valv objekt, till exempel nycklar, hemligheter och certifikat.

- [Använda Azure Key Vault ' mjuk borttagning '](../key-vault/general/key-vault-recovery.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security benchmark: incident svar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: utveckla en incident svars guide för din organisation. Se till att det finns skriftliga svars planer för incidenter som definierar alla roller för personal samt de olika faserna av incident hantering och hantering från identifiering till granskning efter incidenten. 

- [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Använd NIST hanterings guide för dator säkerhet för att hjälpa till med att skapa din egen incident svars plan](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till varje avisering för att kunna prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.
Markera prenumerationer tydligt (till exempel produktion, icke-produktion) och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser.

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems incident svars funktioner på en vanlig takt. Identifiera svaga punkter och luckor, och ändra planen efter behov.
- [Läs NIST för att testa, träna och träna program för IT-planer och funktioner](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har öppnats av en olaglig eller obehörig part. Granska incidenter när du är säker på att problemen är lösta.
- [Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Använd Azure Security Center Data Connector för att strömma aviseringarna till Azure Sentinel enligt organisationens krav.

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöden i Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer.
- [Konfigurera automatisering av arbets flöden och Logic Apps](../security-center/workflow-automation.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstester och Red Team-tester

*Mer information finns i [övningen för Azure Security benchmark: inträngande tester och röda team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program. 

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)