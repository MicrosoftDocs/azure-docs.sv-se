---
title: Azure-säkerhetsbaslinje för Azure Monitor
description: Den Azure Monitor säkerhetsbaslinjen innehåller procedurvägledning och resurser för att implementera säkerhetsrekommendationerna som anges i Azure Security Benchmark.
author: msmbaldwin
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f002c7196b864d4a04beda1124d0519af612b716
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600241"
---
# <a name="azure-security-baseline-for-azure-monitor"></a>Azure-säkerhetsbaslinje för Azure Monitor

Den här säkerhetsbaslinjen tillämpar riktlinjer [från Azure Security Benchmark version 1.0](../security/benchmarks/overview-v1.md) Azure Monitor. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure. Innehållet grupperas efter de säkerhetskontroller **som definierats** av Azure Security Benchmark och tillhörande vägledning som gäller för Azure Monitor. **Kontroller** som inte Azure Monitor, eller för vilka ansvaret är Microsofts, har undantagits.

Information om hur Azure Monitor mappning helt till Azure Security Benchmark finns i den fullständiga Azure Monitor [säkerhetsbaslinjemappningsfilen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Skydda Azure-resurser i virtuella nätverk

**Vägledning:** Aktivera Azure Private Link att tillåta åtkomst till Azure SaaS-tjänster (till exempel Azure Monitor) och Azure-värdtjänster för kunder/partner via en privat slutpunkt i ditt virtuella nätverk. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet.

Använd tjänsttaggarna "AzureMonitor" för att tillåta inkommande och utgående trafik via nätverkssäkerhetsgrupper för att tillåta att trafik når Azure Monitor trafik. Om du vill tillåta att testtrafiken för tillgänglighetsövervakning når Azure Monitor du tjänsttaggen "ApplicationInsightsAvailability" för all inkommande trafik via nätverkssäkerhetsgrupper. Om du vill tillåta att aviseringsmeddelanden når kundernas slutpunkter använder du tjänsttaggen "ActionGroup" för att tillåta inkommande trafik via nätverkssäkerhetsgrupper.

Regler för virtuellt nätverk Azure Monitor att endast acceptera kommunikation som skickas från valda undernät i ett virtuellt nätverk.

Använd Log Analytics-gatewayen för att skicka data till en Log Analytics-arbetsyta i Azure Monitor för de datorer som inte kan ansluta direkt till Internet, vilket förhindrar att datorer måste vara anslutna till Internet. 

- [Konfigurera en Private Link för Azure Monitor](/azure/azure-monitor/platform/private-link-security)

- [Ansluta datorer utan Internetåtkomst med hjälp av Log Analytics-gatewayen i Azure Monitor](/azure/azure-monitor/platform/gateway)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Övervaka och logga konfiguration och trafik för virtuella nätverk, undernät och nätverksgränssnitt

**Vägledning:** Azure Monitor är en kärntjänst och inte stöder distribution direkt till ett virtuellt nätverk, dess underliggande infrastruktur hanteras av Microsoft. Men för resurser som gör nätverksanslutningar till Azure Monitor ska du skydda nätverket med en nätverkssäkerhetsgrupp. Aktivera flödesloggar för nätverkssäkerhetsgruppen och skicka loggar till ett lagringskonto för trafikgranskning. Du kan också skicka flödesloggar till en Log Analytics-arbetsyta och använda Trafikanalys för att ge insikter om trafikflödet i ditt Azure-moln. Vissa fördelar med Trafikanalys är möjligheten att visualisera nätverksaktivitet och identifiera hotpunkter, identifiera säkerhetshot, förstå trafikflödesmönster och hitta felkonfigurationer i nätverk.

När du använder Azure Monitor med Private Link får du åtkomst till nätverksloggning, till exempel "Data som bearbetas av den privata slutpunkten (IN/OUT)".

- [Nätverkskrav för Azure Monitor agenter](/azure/azure-monitor/platform/log-analytics-agent#network-requirements)

- [Ansluta datorer utan Internetåtkomst med hjälp av Log Analytics-gatewayen i Azure Monitor](/azure/azure-monitor/platform/gateway)

- [Så här aktiverar du flödesloggar för nätverkssäkerhetsgrupp](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

- [Förstå nätverkssäkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexiteten och det administrativa arbetet med nätverkssäkerhetsregler

**Vägledning:** Använd tjänsttaggarna "AzureMonitor" för att tillåta inkommande och utgående trafik genom nätverkssäkerhetsgrupper om du vill tillåta att trafik når Azure Monitor trafik via tjänsttaggarna "AzureMonitor". Om du vill tillåta att testtrafiken för tillgänglighetsövervakning når Azure Monitor använder du tjänsttaggen "ApplicationInsightsAvailability" för all inkommande trafik via nätverkssäkerhetsgrupper. Microsoft hanterar de adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras.

- [Förstå och använda tjänsttaggar](../virtual-network/service-tags-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentera trafikkonfigurationsregler

**Vägledning:** Azure Monitor är en del av Azure-kärntjänsterna och kan inte distribueras som en tjänst separat. Azure Monitor-komponenter, inklusive Azure Monitor Agent och Application Insights SDK kan distribueras med dina resurser, vilket kan påverka resursernas säkerhetsstatus.

- [Nätverkskrav för Azure Monitor agenter](/azure/azure-monitor/platform/log-analytics-agent#network-requirements)

- [Ansluta datorer utan Internetåtkomst med hjälp av Log Analytics-gatewayen i Azure Monitor](/azure/azure-monitor/platform/gateway) 

- [Se komma igång med Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview#get-started)

- [Konfigurera webbtester för tillgänglighet](app/monitor-web-app-availability.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

**Vägledning:** Använd Azure-aktivitetsloggen för att övervaka resurskonfigurationer och identifiera ändringar i dina nätverksresurser relaterade till Azure Monitor. Skapa aviseringar inom Azure Monitor som utlöses när ändringar av dessa viktiga nätverksresurser sker.

- [Så här visar och hämtar du Händelser i Azure-aktivitetsloggen](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [Prestandatest för Azure-säkerhet: Loggning och övervakning.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera central hantering av säkerhetsloggar

**Vägledning:** Azure Monitor använder aktivitetsloggar för att logga ändringar i dess resurser. Du kan exportera dessa loggar till Azure Storage, Event Hub eller en Log Analytics-arbetsyta. Mata in loggar via Azure Monitor för att aggregera säkerhetsdata som genereras av slutpunktsenheter, nätverksresurser och andra säkerhetssystem. Inom Azure Monitor kan du köra frågor mot och utföra analyser mot data, använda Azure Storage-konton för all långsiktig/arkiveringslagring av loggar.

Du kan också aktivera och ta med data till Azure Sentinel siem eller siem från tredje part.

- [Så här samlar du in plattformsloggar och mått med Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)

- [Så här samlar du in interna värdloggar för virtuella Azure-datorer med Azure Monitor](/azure/azure-monitor/learn/quick-collect-azurevm)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Komma igång med Azure Monitor SIEM-integrering från tredje part](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – microsoft.insights:**

[!INCLUDE [Resource Policy for microsoft.insights 2.2](../../includes/policy/standards/asb/rp-controls/microsoft.insights-2-2.md)]

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägledning:** Azure Monitor använder aktivitetsloggar, aktivitetsloggen aktiveras automatiskt och loggar åtgärder som vidtas på Azure Monitor-resurser, till exempel: vem som startade åtgärden, när åtgärden inträffade, status för åtgärden och annan användbar granskningsinformation. 

- [Så här samlar du in plattformsloggar och mått med Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)

- [Förstå loggning och olika loggtyper i Azure](/azure/azure-monitor/platform/platform-logs-overview)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – microsoft.insights:**

[!INCLUDE [Resource Policy for microsoft.insights 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.insights-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera kvarhållning av säkerhetslogglagring

**Vägledning:** I Azure Monitor anger du kvarhållningsperioden för Log Analytics-arbetsytan enligt organisationens efterlevnadsregler. Använd Azure Storage-konton för all långsiktig/arkiveringslagring av dina loggar.

- [Ändra kvarhållningsperioden för data i Log Analytics](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Så här konfigurerar du bevarandeprincip för Azure Storage-kontologgar](/azure/storage/common/storage-monitor-storage-account#configure-logging)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning:** Analysera och övervaka loggar för avvikande beteende och granska resultaten regelbundet. Använd Azure Monitor och en Log Analytics-arbetsyta för att granska loggar och köra frågor på loggdata.

Du kan också aktivera och ta med data till Azure Sentinel eller en SIEM från tredje part.

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Komma igång med Log Analytics-frågor](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Så här utför du anpassade frågor i Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivera aviseringar för avvikande aktiviteter

**Vägledning:** Använd Azure Security Center Log Analytics-arbetsyta för övervakning och avisering om avvikande aktivitet som finns i säkerhetsloggar och händelser. Du kan också aktivera och ta med data för att Azure Sentinel.

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Hantera aviseringar i Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Så här varnar du om loggdata i Log Analytics](/azure/azure-monitor/learn/tutorial-response)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Upprätthålla en förteckning över administrativa konton

**Vägledning:** Med rollbaserad åtkomstkontroll i Azure (Azure RBAC) kan du hantera åtkomsten till Azure-resurser via rolltilldelningar. Du kan tilldela dessa roller till användare, grupper av tjänstens huvudnamn och hanterade identiteter. Det finns fördefinierade inbyggda roller för vissa resurser och dessa roller kan inventeras eller frågas via verktyg som Azure CLI, Azure PowerShell eller Azure-portalen.

- [Så här får du en katalogroll i Azure Active Directory (Azure AD) med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Hämta medlemmar i en katalogroll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Använd dedikerade administrativa konton

**Vägledning:** Skapa standardprocedurer för användning av dedikerade administrativa konton. Använd Azure Security Center Identitets- och åtkomsthantering för att övervaka antalet administrativa konton.

Du kan också aktivera just-in-time/just-enough-åtkomst med hjälp av Azure Active Directory (Azure AD) Privileged Identity Management privilegierade roller för Microsoft-tjänster och Azure Resource Manager.

- [Azure AD Privileged Identity Management översikt](../active-directory/privileged-identity-management/pim-configure.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory för enkel inloggning (SSO)

**Vägledning:** Använd vid behov Azure Active Directory enkel inloggning (Azure AD) i stället för att konfigurera enskilda fristående autentiseringsuppgifter per tjänst. Använd Azure Security Center rekommendationer för identitets- och åtkomsthantering.

- [Förstå enkel inloggning med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använd multifaktorautentisering för Azure Active Directory åtkomstbaserad åtkomst

**Vägledning:** Aktivera Azure Active Directory (Azure AD) multifaktorautentisering och följ Azure Security Center rekommendationer för identitet och åtkomst.

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst inom Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Använd dedikerade datorer (arbetsstationer för privilegierad åtkomst) för alla administrativa uppgifter

**Vägledning:** Använd en säker, Azure-hanterad arbetsstation (även kallad arbetsstation för privilegierad åtkomst eller PAW) för administrativa uppgifter som kräver utökade privilegier.

- [Förstå säkra, Azure-hanterade arbetsstationer](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Så här aktiverar du Azure Active Directory multifaktorautentisering (Azure AD)](../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logga och avisering om misstänkta aktiviteter från administrativa konton

**Vägledning:** Använd Azure Active Directory (Azure AD) säkerhetsrapporter och övervakning för att identifiera när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure Security Center för att övervaka identitets- och åtkomstaktivitet.

- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här övervakar du användarnas identitets- och åtkomstrelaterade aktiviteter i Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Hantera Azure-resurser från endast godkända platser

**Vägledning:** Använd villkorsstyrd åtkomst med namngivna platser för att endast tillåta åtkomst från specifika logiska gruppningar av IP-adressintervall eller länder/regioner.

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning:** Använd Azure Active Directory (Azure AD) som centralt system för autentisering och auktorisering. Azure AD skyddar data med hjälp av stark kryptering för data i vila och under överföring. Azure AD saltar, hashar och lagrar användarautentiseringsuppgifter på ett säkert sätt.

- [Skapa och konfigurera en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Granska och stämma av användaråtkomst regelbundet

**Vägledning:** Azure Active Directory (Azure AD) innehåller loggar som hjälper dig att identifiera inaktuella konton. Använd dessutom Azure Identity Access Reviews för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Användaråtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

- [Förstå Azure AD-rapportering](/azure/active-directory/reports-monitoring/)

- [Så här använder du Azure Identity Access Reviews](../active-directory/governance/access-reviews-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning:** Du har åtkomst till Azure Active Directory(Azure AD) inloggningsaktivitet, gransknings- och riskhändelseloggkällor, vilket gör att du kan integrera med valbara SIEM-/övervakningsverktyg. Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka granskningsloggar och inloggningsloggar till en Log Analytics-arbetsyta. Du kan konfigurera önskade aviseringar i Log Analytics-arbetsytan.

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Avisering om kontots beteendeavvikelse vid inloggning

**Vägledning:** Använd Azure Active Directory (Azure AD) Risk- och Identity Protection-funktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder relaterade till användaridentiteter. Du kan också mata in data i Azure Sentinel för ytterligare undersökning.

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du riskprinciper för Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Upprätthålla en förteckning över känslig information

**Vägledning:** Använd taggar när det är möjligt för att spåra Azure Monitor som lagrar eller bearbetar känslig information som dina Log Analytics-arbetsytor.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

- [Hantera åtkomst till loggdata och arbetsytor i Azure Monitor](/azure/azure-monitor/platform/manage-access)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolera system som lagrar eller bearbetar känslig information

**Vägledning:** Implementera isolering med separata prenumerationer och hanteringsgrupper för enskilda säkerhetsdomäner, till exempel miljötyp och känslighetsnivå för data. Du kan begränsa åtkomstnivån till dina Azure Monitor relaterade resurser som dina program och företagsmiljöer kräver. Du kan styra åtkomsten till Azure Monitor via rollbaserad åtkomstkontroll i Azure (Azure RBAC).

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Kryptera all känslig information under överföring

**Vägledning:** Azure Monitor förhandlar TLS 1.2 som standard. Se till att alla klienter som ansluter till dina Azure-resurser kan förhandla TLS 1.2 eller högre. 

Application Insights och Log Analytics fortsätter båda att tillåta att TLS 1.1- och TLS 1.0-data matas in. Data kan begränsas till TLS 1.2 genom konfiguration på klientsidan.

- [Så här skickar du data på ett säkert sätt med TLS 1.2](/azure/azure-monitor/platform/data-security#sending-data-securely-using-tls-12)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Använd ett aktivt identifieringsverktyg för att identifiera känsliga data

**Vägledning:** Funktioner för dataidentifiering, klassificering och förlustskydd är ännu inte tillgängliga för Azure Monitor. Implementera en lösning från tredje part om det behövs i efterlevnadssyfte.
För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt och tar lång tid att skydda mot förlust och exponering av kunddata. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och upprätthåller en uppsättning robusta dataskyddskontroller och -funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Använd rollbaserad åtkomstkontroll för att styra åtkomsten till resurser

**Vägledning:** Använda rollbaserad åtkomstkontroll (RBAC) i Azure för att hantera åtkomst Azure Monitor.

- [Roller, behörigheter och säkerhet i Azure Monitor](/azure/azure-monitor/platform/roles-permissions-security)

- [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Kryptera känslig information i vila

**Vägledning:** Azure Monitor säkerställer att alla data och sparade frågor krypteras i vila med microsoft-hanterade nycklar (MMK). Azure Monitor också ett alternativ för kryptering med din egen nyckel som lagras i din Azure Key Vault och används av lagring med hjälp av systemautentisering för hanterad identitet. Den här kund hanterade nyckeln (CMK) kan vara antingen programvara eller maskinvaruskyddad HSM.

- [Azure Monitor kund hanterade nycklar](/azure/azure-monitor/platform/customer-managed-keys)

- [Log Analytics-datasäkerhet](/azure/azure-monitor/platform/data-security)

- [Datainsamling, kvarhållning och lagring i Application Insights](app/data-retention-privacy.md)

- [Förstå kryptering vid vila i Azure](../security/fundamentals/encryption-atrest.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisering om ändringar av viktiga Azure-resurser

**Vägledning:** Använd Azure Monitor med Azure-aktivitetsloggen för att skapa aviseringar för när ändringar sker i Azure Monitor och relaterade resurser.

- [Så här skapar du aviseringar för Händelser i Azure-aktivitetsloggen](/azure/azure-monitor/platform/alerts-activity-log)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – microsoft.insights:**

[!INCLUDE [Resource Policy for microsoft.insights 4.9](../../includes/policy/standards/asb/rp-controls/microsoft.insights-4-9.md)]

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Använd en riskklassificeringsprocess för att prioritera åtgärder för identifierade säkerhetsrisker

**Vägledning:** Använd ett vanligt riskbedömningsprogram (till exempel Common Vulnerability Scoring System) eller standardriskklassificeringarna som tillhandahålls av ditt genomsökningsverktyg från tredje part.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security Benchmark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Använd automatisk tillgångsidentifiering

**Vägledning:** Använd Azure CLI för att köra frågor mot och identifiera Azure Monitor resurser i dina prenumerationer. Se till att du har rätt (läsbehörighet) i din klientorganisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

- [Azure Monitor CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest&amp;preserve-view=true)

- [Så här visar du dina Azure-prenumerationer](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

- [Roller, behörigheter och säkerhet i Azure Monitor](/azure/azure-monitor/platform/roles-permissions-security)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="62-maintain-asset-metadata"></a>6.2: Underhålla tillgångsmetadata

**Vägledning:** Tillämpa taggar på Azure Monitor-resurser som ger metadata för att organisera dem logiskt i en taxonomi.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ta bort obehöriga Azure-resurser

**Vägledning:** Använd taggar, hanteringsgrupper och separata prenumerationer när det är lämpligt för att organisera och spåra Azure Monitor relaterade resurser. Stämma av inventeringen regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definiera och underhålla inventering av godkända Azure-resurser

**Vägledning:** Skapa en förteckning över godkända Azure-resurser och godkänd programvara för beräkningsresurser enligt organisationens behov.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Övervaka för ej godkända Azure-resurser

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i dina prenumerationer.

Använd Azure Resource Graph till att fråga efter och identifiera resurser i prenumerationerna.  Se till att alla Azure-resurser som finns i miljön godkänns.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Skapa frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Ta bort ej godkända Azure-resurser och program

**Vägledning:** Stämma av inventeringen regelbundet och se till att obehöriga Azure Monitor relaterade resurser tas bort från prenumerationen inom rimlig tid.  

- [Ta bort Azure Log Analytics-arbetsyta](/azure/azure-monitor/platform/delete-workspace)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="69-use-only-approved-azure-services"></a>6.9: Använd endast godkända Azure-tjänster

**Vägledning:** Använd Azure Policy att begränsa vilka Azure Monitor relaterade resurser som du kan etablera i din miljö.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en viss resurstyp med Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning:** Använd villkorsstyrd åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resources Manager genom att konfigurera "Blockera åtkomst" för Microsoft Azure-hanteringsappen.

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resources Manager](../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security Benchmark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning:** Använd anpassade Azure Policy för att granska eller framtvinga konfigurationen av Azure Monitor relaterade resurser. Du kan också använda inbyggda definitioner Azure Policy tillgängliga.

Dessutom Azure Resource Manager kan exportera mallen i JavaScript Object Notation (JSON), som bör granskas för att säkerställa att konfigurationerna uppfyller/överskrider säkerhetskraven för din organisation.

Du kan också använda rekommendationer från Azure Security Center som en säker konfigurationsbaslinje för dina Azure-resurser.

Om du använder APM-funktioner för liveuppspelning ska du skydda kanalen med en hemlig API-nyckel utöver instrumenteringsnyckeln.

- [Säker APM-Live Metrics Stream](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel)

- [Så här visar du tillgängliga Azure Policy-alias](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Självstudie: Skapa och hantera principer för att framtvinga efterlevnad](../governance/policy/tutorials/create-and-manage.md)

- [Export av en enskild resurs och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Säkerhetsrekommendationer – en referensguide](../security-center/recommendations-reference.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Underhålla säkra Azure-resurskonfigurationer

**Vägledning:** Använd Azure Policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar för Azure Monitor relaterade resurser.  Dessutom kan du använda mallar Azure Resource Manager för att upprätthålla säkerhetskonfigurationen för dina Azure Monitor resurser som krävs av din organisation.

- [Förstå Azure Policy effekter](../governance/policy/concepts/effects.md)

- [Skapa och hantera principer för att använda kompatibilitet](../governance/policy/tutorials/create-and-manage.md)

- [översikt Azure Resource Manager mallar](../azure-resource-manager/templates/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Lagra konfiguration av Azure-resurser på ett säkert sätt

**Vägledning:** Använda Azure DevOps för att lagra och hantera din kod på ett säkert sätt, till exempel anpassade Azure-principer Azure Resource Manager mallar. För att komma åt de resurser som du hanterar i Azure DevOps kan du bevilja eller neka behörigheter till specifika användare, inbyggda säkerhetsgrupper eller grupper som definierats i Azure Active Directory (Azure AD) om de är integrerade med Azure DevOps eller Active Directory om de är integrerade med TFS.

- [Lagra kod i Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Om behörigheter och grupper i Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuera konfigurationshanteringsverktyg för Azure-resurser

**Vägledning:** Definiera och implementera standardsäkerhetskonfigurationer för Azure Monitor relaterade resurser med hjälp av Azure Policy. Använd anpassade Azure Policy för att granska eller framtvinga säkerhetskonfigurationen för dina Azure Monitor relaterade resurser. Du kan också använda inbyggda principdefinitioner som är relaterade till dina specifika resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy alias](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementera automatisk konfigurationsövervakning för Azure-resurser

**Vägledning:** Använd Azure Security Center för att utföra baslinjesökningar för dina Azure Monitor relaterade resurser.  Du kan också använda Azure Policy för att varna och granska Azure-resurskonfigurationer.

- [Så här åtgärdar du rekommendationer i Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning:** Använd hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda hemlighetshantering för Azure Monitor-relaterade resurser som stöds.

- [Så här integrerar du med azure-hanterade identiteter](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Tjänster som stöder hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Så här skapar du en Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Så här tillhandahåller du Key Vault autentisering med en hanterad identitet](/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Hantera identiteter på ett säkert och automatiskt sätt

**Vägledning:** Använda hanterade identiteter för att förse Azure-tjänster med en automatiskt hanterad identitet Azure Active Directory (Azure AD). Med hanterade identiteter kan du autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Azure Monitor resurser, utan autentiseringsuppgifter i koden.

- [Så här konfigurerar du hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminera oavsiktlig exponering av autentiseringsuppgifter

**Vägledning:** Implementera skanner för autentiseringsuppgifter för att identifiera autentiseringsuppgifter i koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault.

- [Konfigurera skanner för autentiseringsuppgifter](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security Benchmark: Malware Defense.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Skanna filer i förväg som ska laddas upp till Icke-beräkningsbaserade Azure-resurser

**Vägledning:** Microsoft Anti-malware är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure Monitor relaterade resurser), men den körs inte på ditt innehåll. 

Försök alla filer som laddas upp till tillämpliga Azure Monitor relaterade resurser, till exempel Log Analytics-arbetsyta.

Använd Azure Security Center hotidentifiering för datatjänster för att identifiera skadlig kod som överförs till lagringskonton. 

- [Förstå Microsoft Anti-malware för Azure Cloud Services och Virtual Machines](../security/fundamentals/antimalware.md)

- [Förstå Azure Security Center hotidentifiering för datatjänster](../security-center/azure-defender.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Prestandatest för Azure-säkerhet: Dataåterställning.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Säkerställ regelbundna automatiserade säkerhetskopieringar

**Vägledning:** Använd Azure Resource Manager för att exportera Azure Monitor och relaterade resurser i en JavaScript Object Notation-mall (JSON) som kan användas som säkerhetskopia för Azure Monitor och relaterade konfigurationer.  Använd Azure Automation för att köra säkerhetskopieringsskripten automatiskt. 

- [Hantera Log Analytics-arbetsyta med Azure Resource Manager mallar](/azure/azure-monitor/samples/resource-manager-workspace)

- [Export av en enskild resurs och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Om Azure Automation](../automation/automation-intro.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utför fullständiga systemsäkerhetskopior och säkerhetskopiera kund hanterade nycklar

**Vägledning:** Använd Azure Resource Manager för att exportera Azure Monitor och relaterade resurser i en JavaScript Object Notation-mall (JSON) som kan användas som säkerhetskopia för Azure Monitor och relaterade konfigurationer. Säkerhetskopiera kund hanterade nycklar Azure Key Vault om Azure Monitor resurser använder kund hanterade nycklar,

- [Hantera Log Analytics-arbetsyta med Azure Resource Manager mallar](/azure/azure-monitor/platform/template-workspace-configuration)

- [Export av en enskild resurs och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Säkerhetskopiera nyckelvalvsnycklar i Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Verifiera alla säkerhetskopior, inklusive kund hanterade nycklar

**Vägledning:** Se till att du regelbundet kan utföra återställning med Azure Resource Manager mallfiler. Testa återställning av säkerhetskopierade kund hanterade nycklar.

- [Hantera Log Analytics-arbetsyta med hjälp Azure Resource Manager mallar](/azure/azure-monitor/samples/resource-manager-workspace)

- [Återställa nyckelvalvsnycklar i Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Skydda säkerhetskopior och kund hanterade nycklar

**Vägledning:** Använd Azure DevOps för att lagra och hantera din kod på ett säkert sätt, till exempel anpassade Azure-principer Azure Resource Manager mallar. För att skydda resurser som du hanterar i Azure DevOps kan du bevilja eller neka behörigheter till specifika användare, inbyggda säkerhetsgrupper eller grupper som definierats i Azure Active Directory (Azure AD) om de är integrerade med Azure DevOps eller Active Directory om de är integrerade med TFS.  Använd rollbaserad åtkomstkontroll i Azure (Azure RBAC) för att skydda kund hanterade nycklar.

Dessutom kan aktivera Soft-Delete rensningsskydd i en Key Vault att skydda nycklar mot oavsiktlig eller skadlig borttagning. Om Azure Storage används för att lagra Azure Resource Manager säkerhetskopior aktiverar du mjuk borttagning för att spara och återställa data när blobar eller blobögonblicksbilder tas bort.

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Om behörigheter och grupper i Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Så här aktiverar Soft-Delete och rensar skydd i Key Vault](../storage/blobs/soft-delete-blob-overview.md)

- [Mjuk borttagning för Azure Storage-blobar](../storage/blobs/soft-delete-blob-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Skapa en guide för incidentsvar

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.

- [Vägledning om hur du skapar en egen process för svar på säkerhetsincidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center är en incidents uppbyggnad](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Använd NIST:s guide för hantering av datorsäkerhetsincidenter för att underlätta skapandet av din egen plan för incidenthantering](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Skapa en procedur för incidentbedömning och prioritering

**Vägledning:** Security Center tilldelar en allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som ska undersökas först. Allvarlighetsgraden baseras på hur säker Security Center är i hitta eller analys som används för att utfärda aviseringen samt förtroendenivån för att det fanns skadlig avsikt bakom aktiviteten som ledde till aviseringen.

Markera dessutom prenumerationer tydligt (t.ex. produktion, icke-produktion) med hjälp av taggar och skapa ett namngivningssystem för att tydligt identifiera och kategorisera Azure-resurser, särskilt de som bearbetar känsliga data.  Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="103-test-security-response-procedures"></a>10.3: Testa procedurer för säkerhetssvar

**Vägledning:** Genomför övningar för att regelbundet testa systemets incidentsvarsfunktioner för att skydda dina Azure-resurser. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [NIST:s publikation – Guide till test-, tränings- och övningsprogram för IT-planer och IT-funktioner](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Ange kontaktuppgifter för säkerhetsincidenter och konfigurera aviseringsmeddelanden för säkerhetsincidenter

**Vägledning:** Kontaktinformation om säkerhetsincidenter används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en obehörig part. Granska incidenter i efterhand för att säkerställa att problemen är lösta.

- [Ställa in Azure Security Center säkerhetskontakt](../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Införliva säkerhetsaviseringar i systemet för incidentsvar

**Vägledning:** Exportera dina Azure Security Center och rekommendationer med hjälp av funktionen Kontinuerlig export för att identifiera risker för Azure-resurser. Med löpande export kan du exportera aviseringar och rekommendationer manuellt eller kontinuerligt. Du kan använda anslutningsappen Azure Security Center data för att strömma aviseringarna till Azure Sentinel. 

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md) 

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisera svaret på säkerhetsaviseringar

**Vägledning:** Använd funktionen Arbetsflödesautomation i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" för säkerhetsaviseringar och rekommendationer för att skydda dina Azure-resurser.

- [Så här konfigurerar du arbetsflödesautomation och Logic Apps](../security-center/workflow-automation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [Azure Security Benchmark: Penetration Tests (Intrångstester) och Red Team Exercises (Red Team-övningar).](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Genomför regelbundna intrångstester av dina Azure-resurser och se till att alla viktiga säkerhetsresultat åtgärdas

**Vägledning:** Följ Microsofts regler för engagemang för att säkerställa att dina intrångstester inte bryter mot Microsofts principer. Använd Microsofts strategi och körning av Red Teaming och intrångstester på live-webbplatser mot Microsoft-hanterad molninfrastruktur, -tjänster och -program.

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
