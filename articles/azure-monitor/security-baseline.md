---
title: Azures säkerhets bas linje för Azure Monitor
description: Azure Monitor säkerhets bas linje ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: eba05ce7e7575ec773a4796d87bdd82168fe5b1d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101725660"
---
# <a name="azure-security-baseline-for-azure-monitor"></a>Azures säkerhets bas linje för Azure Monitor

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 1,0](../security/benchmarks/overview-v1.md) till Azure Monitor. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure.
Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för Azure Monitor. **Kontroller** som inte är tillämpliga på Azure Monitor har uteslutits. 

 
Om du vill se hur Azure Monitor helt mappar till Azures säkerhets mätning, se den [fullständiga Azure Monitor mappnings filen för säkerhets bas linjen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: Aktivera Azures privata länk om du vill tillåta åtkomst till Azure SaaS Services (till exempel Azure Monitor) och Azure-värdbaserade kund-/partner tjänster över en privat slut punkt i det virtuella nätverket. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet.

Om du vill tillåta att trafik når Azure Monitor använder du tjänst taggarna "AzureMonitor" för att tillåta inkommande och utgående trafik via nätverks säkerhets grupper. Om du vill tillåta att tillgänglighets övervaknings test trafik når Azure Monitor använder du tjänst tag gen "ApplicationInsightsAvailability" till all inkommande trafik via nätverks säkerhets grupper. Om du vill tillåta att aviserings meddelanden når kund slut punkter använder du tjänst tag gen "ActionGroup" för att tillåta inkommande trafik via nätverks säkerhets grupper.

Regler för virtuella nätverk gör det möjligt för Azure Monitor att endast acceptera kommunikation som skickas från valda undernät i ett virtuellt nätverk.

Använd Log Analytics Gateway för att skicka data till en Log Analytics arbets yta i Azure Monitor på uppdrag av datorer som inte kan ansluta direkt till Internet för att förhindra att datorer måste vara anslutna till Internet. 

- [Så här konfigurerar du en privat länk för Azure Monitor](/azure/azure-monitor/platform/private-link-security)

- [Ansluta datorer utan Internet åtkomst med hjälp av Log Analytics gateway i Azure Monitor](/azure/azure-monitor/platform/gateway)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverks gränssnitt

**Vägledning**: Azure Monitor är en kärn tjänst och inte stöder distribution direkt till ett virtuellt nätverk. den underliggande infrastrukturen hanteras av Microsoft. För resurser som gör nätverks anslutningar till Azure Monitor erbjudandet skyddar du dock nätverket med en nätverks säkerhets grupp. Aktivera flödes loggar för nätverks säkerhets grupper och skicka loggar till ett lagrings konto för trafik granskning. Du kan också skicka flödes loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflödet i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

När du använder Azure Monitor med privat länk får du åtkomst till nätverks loggning, till exempel data som bearbetas av den privata slut punkten (IN/ut).

- [Nätverks krav för Azure Monitor agenter](/azure/azure-monitor/platform/log-analytics-agent#network-requirements)

- [Ansluta datorer utan Internet åtkomst med hjälp av Log Analytics gateway i Azure Monitor](/azure/azure-monitor/platform/gateway)

- [Så här aktiverar du flödes loggar för nätverks säkerhets grupper](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

- [Förstå nätverks säkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: om du vill tillåta att trafik når Azure Monitor använder du tjänst taggarna "AzureMonitor" för att tillåta inkommande och utgående trafik via nätverks säkerhets grupper. Om du vill tillåta att tillgänglighets övervaknings test trafik når Azure Monitor använder du tjänst tag gen "ApplicationInsightsAvailability" till all inkommande trafik via nätverks säkerhets grupper. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

- [Förstå och använda service märken](../virtual-network/service-tags-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Azure Monitor är en del av Azure Core-tjänsterna och kan inte distribueras som en tjänst separat. Azure Monitor-komponenter, inklusive Azure Monitor agenten, och Application Insights SDK kan distribueras med dina resurser och detta kan påverka position av dessa resurser.

- [Nätverks krav för Azure Monitor agenter](/azure/azure-monitor/platform/log-analytics-agent#network-requirements)

- [Ansluta datorer utan Internet åtkomst med hjälp av Log Analytics gateway i Azure Monitor](/azure/azure-monitor/platform/gateway) 

- [Se komma igång med Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview#get-started)

- [Så här konfigurerar du webb test för tillgänglighet](app/monitor-web-app-availability.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka datorkonfigurationer och identifiera ändringar i nätverks resurser som är relaterade till Azure Monitor. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av de kritiska nätverks resurserna sker.

- [Visa och hämta Azure aktivitets logg händelser](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets benchmark för Azure: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: Azure Monitor använder aktivitets loggar för att logga ändringar i resurserna. Du kan exportera loggarna till Azure Storage, Event Hub eller en Log Analytics arbets yta. Mata in loggar via Azure Monitor för att samla in säkerhets data som genererats av slut punkts enheter, nätverks resurser och andra säkerhets system. I Azure Monitor kan du fråga och utföra analyser mot data, använda Azure Storage-konton för långsiktig lagring av loggar.

Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM från tredje part.

- [Samla in plattforms loggar och mått med Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)

- [Så här samlar du in interna värd loggar för virtuella Azure-datorer med Azure Monitor](/azure/azure-monitor/learn/quick-collect-azurevm)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Komma igång med Azure Monitor och SIEM-integrering från tredje part](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Ansvar**: Kund

**Azure Security Center övervakning**: [Azures säkerhets benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) är standard policy initiativ för Security Center och är grunden för [Security Center rekommendationer](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy-definitioner som är relaterade till den här kontrollen aktive ras automatiskt av Security Center. Aviseringar som är relaterade till den här kontrollen kan kräva en [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan för de relaterade tjänsterna.

**Azure policy inbyggda definitioner – Microsoft. Insights**:

[!INCLUDE [Resource Policy for microsoft.insights 2.2](../../includes/policy/standards/asb/rp-controls/microsoft.insights-2-2.md)]

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Azure Monitor använder aktivitets loggar aktive ras aktivitets loggen automatiskt och loggar åtgärder som utförs på Azure Monitor resurser, t. ex.: vem som startade åtgärden, när åtgärden utfördes, status för åtgärden och annan användbar gransknings information. 

- [Samla in plattforms loggar och mått med Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)

- [Förstå loggning och olika logg typer i Azure](/azure/azure-monitor/platform/platform-logs-overview)

**Ansvar**: Kund

**Azure Security Center övervakning**: [Azures säkerhets benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) är standard policy initiativ för Security Center och är grunden för [Security Center rekommendationer](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy-definitioner som är relaterade till den här kontrollen aktive ras automatiskt av Security Center. Aviseringar som är relaterade till den här kontrollen kan kräva en [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan för de relaterade tjänsterna.

**Azure policy inbyggda definitioner – Microsoft. Insights**:

[!INCLUDE [Resource Policy for microsoft.insights 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.insights-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: i Azure Monitor anger Log Analytics du arbets ytans lagrings period enligt organisationens regler för efterlevnad. Använd Azure Storage konton för långsiktig/Arkiverad lagring av loggarna.

- [Ändra data lagrings perioden i Log Analytics](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Konfigurera bevarande princip för Azure Storage konto loggar](/azure/storage/common/storage-monitor-storage-account#configure-logging)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: analysera och övervaka loggar för avvikande beteende och granska resultaten regelbundet. Använd Azure Monitor och en Log Analytics arbets yta för att granska loggar och köra frågor på loggdata.

Du kan också aktivera och inaktivera data till Azure Sentinel eller en SIEM från tredje part.

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Komma igång med Log Analytics frågor](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Så här utför du anpassade frågor i Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: Använd Azure Security Center med Log Analytics arbets yta för övervakning och avisering om avvikande aktivitet i säkerhets loggar och händelser. Du kan också aktivera och inaktivera data till Azure Sentinel.

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Hantera aviseringar i Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Så här aviserar du om Log Analytics-loggdata](/azure/azure-monitor/learn/tutorial-response)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security benchmark: identitet och Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: Azure rollbaserad åtkomst kontroll (Azure RBAC) gör att du kan hantera åtkomst till Azure-resurser via roll tilldelningar. Du kan tilldela dessa roller till användare, grupper tjänstens huvud namn och hanterade identiteter. Det finns fördefinierade inbyggda roller för vissa resurser och dessa roller kan inventeras eller frågas via verktyg som Azure CLI, Azure PowerShell eller Azure-portalen.

- [Så här hämtar du en katalog roll i Azure Active Directory (Azure AD) med PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton. Använd Azure Security Center identitets-och åtkomst hantering för att övervaka antalet administrativa konton.

Du kan också aktivera en just-in-Time/bara-tillräcklig-åtkomst genom att använda Azure Active Directory (Azure AD) Privileged Identity Management privilegierade roller för Microsoft-tjänster och Azure Resource Manager.

- [Översikt över Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Använd Azure Active Directory enkel inloggning (SSO)

**Vägledning**: där det är möjligt använder du Azure Active Directory (Azure AD) SSO i stället för att konfigurera enskilda fristående autentiseringsuppgifter per tjänst. Använd rekommendationer för Azure Security Center identitets-och åtkomst hantering.

- [Förstå SSO med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory-baserad åtkomst

**Vägledning**: Aktivera Azure Active Directory (Azure AD) multifaktorautentisering och följ Azure Security Center identitets-och åtkomst rekommendationer.

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd en säker, Azure-hanterad arbets Station (kallas även en privilegie rad åtkomst arbets Station eller Paw) för administrativa uppgifter som kräver förhöjda privilegier.

- [Förstå säkra, Azure-hanterade arbets stationer](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Aktivera Azure Active Directory (Azure AD) multifaktorautentisering](../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: använda Azure Active Directory (Azure AD) säkerhets rapporter och övervakning för att identifiera när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure Security Center för att övervaka identitets-och åtkomst aktiviteter.

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

- [Förstå Azure AD repor ting](/azure/active-directory/reports-monitoring/)

- [Så här använder du granskningar av Azure Identity Access](../active-directory/governance/access-reviews-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: du har åtkomst till Azure Active Directory (Azure AD) inloggnings aktivitet, gransknings-och risk händelse logg källor, som gör att du kan integrera med alla Siem/övervaknings verktyg. Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka gransknings loggar och inloggnings loggar till en Log Analytics-arbetsyta. Du kan konfigurera önskade aviseringar i Log Analytics arbets ytan.

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: varning vid inloggnings beteende för konto

**Vägledning**: Använd Azure Active Directory (Azure AD) risk-och identitets skydds funktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter. Du kan också mata in data i Azure Sentinel för ytterligare undersökning.

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar när det är möjligt att spåra Azure Monitor resurser som lagrar eller bearbetar känslig information som du Log Analytics arbets ytor.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

- [Hantera åtkomst till loggdata och arbetsytor i Azure Monitor](/azure/azure-monitor/platform/manage-access)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera isolering med separata prenumerationer och hanterings grupper för enskilda säkerhets domäner, till exempel miljö typ och data känslighets nivå. Du kan begränsa åtkomst nivån till Azure Monitor och relaterade resurser som dina program och företags miljöer kräver. Du kan styra åtkomsten till Azure Monitor via rollbaserad åtkomst kontroll i Azure (Azure RBAC).

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du hanterings grupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: Azure Monitor förhandlar TLS 1,2 som standard. Se till att alla klienter som ansluter till dina Azure-resurser kan förhandla TLS 1,2 eller senare. 

Application Insights och Log Analytics fortsätter att tillåta att TLS 1,1-och TLS 1,0-data matas in. Data kan begränsas till TLS 1,2 genom att konfigurera på klient sidan.

- [Så här skickar du data på ett säkert sätt med TLS 1,2](/azure/azure-monitor/platform/data-security#sending-data-securely-using-tls-12)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure Monitor. Implementera en lösning från tredje part om det krävs för efterlevnad.
För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: Använd rollbaserad åtkomst kontroll för att kontrol lera åtkomst till resurser

**Vägledning**: Använd Azure rollbaserad åtkomst kontroll (RBAC) för att hantera åtkomst till Azure Monitor.

- [Roller, behörigheter och säkerhet i Azure Monitor](/azure/azure-monitor/platform/roles-permissions-security)

- [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: Azure Monitor säkerställer att alla data och sparade frågor krypteras i vila med hjälp av Microsoft-hanterade nycklar (MMK). Azure Monitor innehåller också ett alternativ för kryptering med hjälp av din egen nyckel som lagras i din Azure Key Vault och som används av lagring med systemtilldelad autentisering med hanterad identitet. Den här Kundhanterade nyckeln (CMK) kan vara antingen program vara eller maskin vara-HSM skyddad.

- [Azure Monitor Kundhanterade nycklar](/azure/azure-monitor/platform/customer-managed-keys)

- [Log Analytics data säkerhet](/azure/azure-monitor/platform/data-security)

- [Datainsamling, kvarhållning och lagring i Application Insights](app/data-retention-privacy.md)

- [Förstå kryptering vid vila i Azure](../security/fundamentals/encryption-atrest.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i Azure Monitor och relaterade resurser.

- [Så här skapar du aviseringar för Azure aktivitets logg händelser](/azure/azure-monitor/platform/alerts-activity-log)

**Ansvar**: Kund

**Azure Security Center övervakning**: [Azures säkerhets benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) är standard policy initiativ för Security Center och är grunden för [Security Center rekommendationer](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy-definitioner som är relaterade till den här kontrollen aktive ras automatiskt av Security Center. Aviseringar som är relaterade till den här kontrollen kan kräva en [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan för de relaterade tjänsterna.

**Azure policy inbyggda definitioner – Microsoft. Insights**:

[!INCLUDE [Resource Policy for microsoft.insights 4.9](../../includes/policy/standards/asb/rp-controls/microsoft.insights-4-9.md)]

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security benchmark: sårbarhet Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: Använd ett gemensamt risk bedömnings program (t. ex. vanliga sårbarhets bedömnings system) eller standard risk klassificeringarna som tillhandahålls av ditt genomsöknings verktyg från tredje part.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure CLI för att fråga och identifiera Azure Monitor resurser i dina prenumerationer. Se till att du har rätt (Läs) behörigheter i din klient organisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

- [Azure Monitor CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest&amp;preserve-view=true)

- [Så här visar du dina Azure-prenumerationer](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

- [Roller, behörigheter och säkerhet i Azure Monitor](/azure/azure-monitor/platform/roles-permissions-security)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar för att Azure Monitor resurser som ger metadata till att logiskt organisera dem i en taxonomi.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer, vid behov, för att ordna och spåra Azure Monitor relaterade resurser. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du hanterings grupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla inventering av godkända Azure-resurser

**Vägledning**: skapa en inventering av godkända Azure-resurser och godkänd program vara för beräknings resurser enligt organisationens behov.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i dina prenumerationer.

Använd Azure Resource Graph till att fråga efter och identifiera resurser i prenumerationerna.  Se till att alla Azure-resurser som finns i miljön är godkända.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Skapa frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: Stäm av inventering regelbundet och se till att obehöriga Azure Monitor relaterade resurser tas bort från prenumerationen inom rimlig tid.  

- [Ta bort Azure Log Analytics-arbetsytan](/azure/azure-monitor/platform/delete-workspace)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att begränsa vilka Azure Monitor relaterade resurser som du kan etablera i din miljö.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azures resurs hanterare genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azures resurs hanterare](../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security benchmark: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: använd anpassade Azure policys definitioner för att granska eller tillämpa konfigurationen för dina Azure Monitor relaterade resurser. Du kan också använda inbyggda Azure Policy definitioner som tillgängliga.

Azure Resource Manager har också möjlighet att exportera mallen i JavaScript Object Notation (JSON), vilken bör granskas för att säkerställa att konfigurationerna uppfyller/överskrider säkerhets kraven för din organisation.

Du kan också använda rekommendationer från Azure Security Center som en säker konfigurations bas linje för dina Azure-resurser.

Om du använder Live streaming APM-funktioner gör du kanalen säker med en hemlig API-nyckel förutom Instrumentation-nyckeln.

- [Skydda APM-Live Metrics Stream](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel)

- [Visa tillgängliga Azure Policy alias](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Självstudie: skapa och hantera principer för att genomdriva efterlevnad](../governance/policy/tutorials/create-and-manage.md)

- [Exportera en och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Säkerhetsrekommendationer – en referensguide](../security-center/recommendations-reference.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns] för att genomdriva säkra inställningar för dina Azure Monitor relaterade resurser.  Dessutom kan du använda Azure Resource Manager mallar för att underhålla säkerhets konfigurationen för dina Azure Monitor-relaterade resurser som krävs av din organisation.

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

- [Skapa och hantera principer för att använda kompatibilitet](../governance/policy/tutorials/create-and-manage.md)

- [Översikt över Azure Resource Manager mallar](../azure-resource-manager/templates/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: Använd Azure DevOps för att lagra och hantera din kod på ett säkert sätt, t. ex. anpassade Azure-principer och Azure Resource Manager mallar. För att få åtkomst till de resurser som du hanterar i Azure DevOps, kan du bevilja eller neka behörigheter till särskilda användare, inbyggda säkerhets grupper eller grupper som definierats i Azure Active Directory (Azure AD) om det är integrerat med Azure DevOps eller Active Directory om det är integrerat med TFS.

- [Så här lagrar du kod i Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Om behörigheter och grupper i Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: definiera och implementera standardkonfigurationer för Azure Monitor relaterade resurser med Azure policy. Använd anpassade Azure Policys definitioner för att granska eller tillämpa säkerhets konfigurationen för dina Azure Monitor relaterade resurser. Du kan också använda inbyggda princip definitioner som är relaterade till dina speciella resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy alias](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: Använd Azure Security Center för att utföra bas linje genomsökningar för dina Azure Monitor relaterade resurser.  Du kan också använda Azure Policy för att varna och granska Azure-resursfiler.

- [Så här åtgärdar du rekommendationer i Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Använd hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda hemlig hantering för Azure Monitor-relaterade resurser som stöds.

- [Integrera med Azure Managed Identities](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Tjänster som stöder hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Så här skapar du en Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Så här ger Key Vault autentisering med en hanterad identitet](/azure/key-vault/general/assign-access=policy-portal)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Med hanterade identiteter kan du autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Azure Monitor resurser, utan några autentiseringsuppgifter i din kod.

- [Konfigurera hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault.

- [Så här konfigurerar du en inloggnings skanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security benchmark: skydd mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som stöder Azure-tjänster (till exempel Azure Monitor relaterade resurser), men det körs inte på ditt innehåll. 

Skanna alla filer som laddas upp till tillämpliga Azure Monitor relaterade resurser, till exempel Log Analytics arbets yta.

Använd Azure Security Center s hot identifiering för data tjänster för att identifiera skadlig kod som laddats upp till lagrings konton. 

- [Förstå Microsofts program mot skadlig kod för Azure Cloud Services och Virtual Machines](../security/fundamentals/antimalware.md)

- [Förstå Azure Security Centers hot identifiering för data tjänster](../security-center/azure-defender.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Azure Security benchmark: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Säkerställ regelbunden automatisk säkerhets kopiering

**Vägledning**: Använd Azure Resource Manager för att exportera Azure Monitor och relaterade resurser i en JavaScript Object Notation (JSON)-mall som kan användas som säkerhets kopia för Azure Monitor och relaterade konfigurationer.  Använd Azure Automation för att köra säkerhets kopierings skripten automatiskt. 

- [Hantera Log Analytics arbets yta med Azure Resource Manager-mallar](/azure/azure-monitor/samples/resource-manager-workspace)

- [Exportera en och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Om Azure Automation](../automation/automation-intro.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: Använd Azure Resource Manager för att exportera Azure Monitor och relaterade resurser i en JavaScript Object Notation (JSON)-mall som kan användas som säkerhets kopia för Azure Monitor och relaterade konfigurationer. Säkerhetskopiera Kundhanterade nycklar i Azure Key Vault om Azure Monitor relaterade resurser använder Kundhanterade nycklar,

- [Hantera Log Analytics arbets yta med Azure Resource Manager-mallar](/azure/azure-monitor/platform/template-workspace-configuration)

- [Exportera en och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Så här säkerhetskopierar du nyckel valv nycklar i Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: Säkerställ att du regelbundet kan utföra återställning med Azure Resource Manager backade mallfiler. Testa återställning av säkerhetskopierade nycklar som hanteras av kunden.

- [Hantera Log Analytics arbets yta med Azure Resource Manager-mallar](/azure/azure-monitor/samples/resource-manager-workspace)

- [Återställa Key Vault-nycklar i Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: Använd Azure-DevOps för att lagra och hantera din kod på ett säkert sätt, t. ex. anpassade Azure-principer Azure Resource Manager mallar. För att skydda resurser som du hanterar i Azure DevOps, kan du bevilja eller neka behörigheter till särskilda användare, inbyggda säkerhets grupper eller grupper som definierats i Azure Active Directory (Azure AD) om det är integrerat med Azure DevOps eller Active Directory om det är integrerat med TFS.  Använd rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att skydda Kundhanterade nycklar.

Aktivera dessutom Soft-Delete och rensa skydd i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning. Om Azure Storage används för att lagra säkerhets kopior av Azure Resource Manager mallar aktiverar du mjuk borttagning för att spara och återställa data när blobbar eller BLOB-ögonblicksbilder tas bort.

- [Så här lagrar du kod i Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Om behörigheter och grupper i Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Aktivera Soft-Delete och rensa skydd i Key Vault](../storage/blobs/soft-delete-blob-overview.md)

- [Mjuk borttagning för Azure Storage-blobar](../storage/blobs/soft-delete-blob-overview.md)

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

Dessutom är det tydligt att markera prenumerationer (t. ex. produktion, icke-Prod.) med hjälp av taggar och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser, särskilt för bearbetning av känsliga data.  Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

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

**Vägledning**: Använd funktionen för arbets flödes automatisering i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer för att skydda dina Azure-resurser.

- [Konfigurera automatisering av arbets flöden och Logic Apps](../security-center/workflow-automation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [övningen för Azure Security benchmark: inträngande tester och röda team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program.

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
