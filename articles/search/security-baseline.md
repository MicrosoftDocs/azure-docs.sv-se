---
title: Azure-säkerhetsbaslinje för Azure Cognitive Search
description: Säkerhetsbaslinjen Azure Cognitive Search innehåller procedurvägledning och resurser för implementering av säkerhetsrekommendationerna som anges i Azure Security Benchmark.
author: msmbaldwin
ms.service: search
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 313ac05d8e4fdc19736d0c35285c2b854ec26968
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589201"
---
# <a name="azure-security-baseline-for-azure-cognitive-search"></a>Azure-säkerhetsbaslinje för Azure Cognitive Search

Den här säkerhetsbaslinjen tillämpar vägledning [från Azure Security Benchmark version 1.0](../security/benchmarks/overview-v1.md) för Azure Cognitive Search. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure. Innehållet grupperas efter de säkerhetskontroller **som definieras** av Azure Security Benchmark och tillhörande vägledning som gäller för Azure Cognitive Search. **Kontroller** som inte Azure Cognitive Search, eller som kunden har exkluderats från.

Information om hur Azure Cognitive Search mappning helt till Azure Security Benchmark finns i den fullständiga [mappningsfilen Azure Cognitive Search säkerhetsbaslinje.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Skydda Azure-resurser i virtuella nätverk

**Vägledning:** Se till att Microsoft Azure Virtual Network undernätsdistributioner har en nätverkssäkerhetsgrupp tillämpad med regler för att implementera ett "minst privilegierat" åtkomstschema. Tillåt endast åtkomst till programmets betrodda portar och IP-adressintervall. Distribuera Azure Cognitive Search med en privat Azure-slutpunkt, där det är möjligt, för att aktivera privat åtkomst till dina tjänster från ditt virtuella nätverk.

Cognitive Search också stöd för ytterligare nätverkssäkerhetsfunktioner för att hantera åtkomstkontrolllistor för nätverk. Konfigurera din söktjänst så att den endast tillåter kommunikation med betrodda källor genom att begränsa åtkomsten från specifika offentliga IP-adressintervall med dess brandväggsfunktion.

- [Så här konfigurerar du privata slutpunkter för Azure Cognitive Search](service-create-private-endpoint.md)

- [Så här konfigurerar du Azure Cognitive Search brandväggen](service-configure-firewall.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Övervaka och logga konfiguration och trafik för virtuella nätverk, undernät och nätverkskort

**Vägledning:** Cognitive Search kan inte distribueras direkt till ett virtuellt nätverk. Men om klientprogrammet eller datakällorna finns i ett virtuellt nätverk kan du övervaka och logga trafik för dessa komponenter i nätverket, inklusive begäranden som skickas till en söktjänst i molnet. Standardrekommendationer är att aktivera en flödeslogg för nätverkssäkerhetsgruppen och skicka loggar till antingen Azure Storage eller en Log Analytics-arbetsyta. Du kan också använda Trafikanalys för att få insikter om trafikmönster.

- [Så här aktiverar du flödesloggar för nätverkssäkerhetsgrupp](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

- [Förstå nätverkssäkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

**Vägledning:** Cognitive Search tillhandahåller inte en specifik funktion för att bekämpa en distribuerad denial-of-service-attack, men du kan aktivera DDoS Protection Standard på de virtuella nätverk som är associerade med din Cognitive Search-tjänst för allmänt skydd.

- [Så här konfigurerar du DDoS-skydd](../ddos-protection/manage-ddos-protection.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="15-record-network-packets"></a>1.5: Registrera nätverkspaket

**Vägledning:** Aktivera flödesloggar för nätverkssäkerhetsgrupper för de nätverkssäkerhetsgrupper som skyddar Azure Virtual Machines (VM) som ska ansluta till din Cognitive Search-tjänst. Skicka loggar till ett Azure Storage konto för trafikgranskning. 

Aktivera Network Watcher paketinfångst om det behövs för att undersöka avvikande aktivitet.

- [Så här aktiverar du NSG-flödesloggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar du Network Watcher](../network-watcher/network-watcher-create.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuera nätverksbaserade system för intrångsidentifiering/intrångsskydd (IDS/IPS)

**Vägledning:** Cognitive Search inte stöder identifiering av nätverksintrång, men som en intrångsminskning kan du konfigurera brandväggsregler för att ange de IP-adresser som accepteras av Cognitive Search tjänsten. Konfigurera en privat slutpunkt för att hålla söktrafik borta från det offentliga Internet.

- [Så här konfigurerar du kund hanterade nycklar för datakryptering](search-security-manage-encryption-keys.md)

- [Så här hämtar du kund hanterad nyckelinformation från index och synonymmappningar](search-security-get-encryption-keys.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexiteten och det administrativa arbetet med nätverkssäkerhetsregler

**Vägledning:** Använd tjänsttaggar, om du använder indexerare och kompetensuppsättningar i Cognitive Search, för att representera ett intervall med IP-adresser som har behörighet att ansluta till externa resurser. 

Tillåt eller neka trafik till resurser genom att ange tjänsttaggnamnet (till exempel AzureCognitiveSearch) i lämpligt käll- eller målfält för en regel. 

- [Tjänsttaggar för virtuellt nätverk](../virtual-network/service-tags-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentera trafikkonfigurationsregler

**Vägledning:** Du kan konfigurera Cognitive Search med en privat Azure-slutpunkt för att integrera din söktjänst med ett virtuellt nätverk.  Använd resurstaggar för nätverkssäkerhetsgrupper och andra resurser som rör nätverkssäkerhet och trafikflöde. För enskilda regler för nätverkssäkerhetsgrupp använder du fältet "Beskrivning" för att dokumentera reglerna som tillåter trafik till/från ett nätverk. 
 

Använd någon av de inbyggda Azure Policy-definitioner som rör taggning, till exempel "Kräv tagg och dess värde"-effekter, för att säkerställa att alla resurser skapas med taggar och för att meddela dig om befintliga otaggade resurser. 

Du kan använda Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.  

 
- [Så här skapar du en privat slutpunkt för Cognitive Search](service-create-private-endpoint.md) 

 
 
- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

- [Så här skapar du en Azure-Virtual Network](../virtual-network/quick-create-portal.md) 

 
- [Så här filtrerar du nätverkstrafik med regler för nätverkssäkerhetsgrupp](../virtual-network/tutorial-filter-network-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [Prestandatest för Azure-säkerhet: Loggning och övervakning.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera central hantering av säkerhetsloggar

**Vägledning:** Mata in loggar relaterade till Cognitive Search via Azure Monitor för att aggregera säkerhetsdata som genereras av slutpunktsenheter, nätverksresurser och andra säkerhetssystem. I Azure Monitor använder du Log Analytics-arbetsytor för att köra frågor mot och utföra analyser och använda Azure Storage-konton för långsiktig lagring och arkiveringslagring. Du kan också aktivera och ta med dessa data för att Azure Sentinel eller en SIEM från tredje part.
 

 
- [Så här kommer du igång Azure Monitor siem-integrering från tredje part](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)
 

 
- [Samla in plattformsloggar och mått med Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 
 

 
- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägledning:** Diagnostikloggar och driftloggar ger inblick i detaljerade åtgärder för Cognitive Search och är användbara för övervakning av tjänsten och för arbetsbelastningar som har åtkomst till din tjänst.  Om du vill samla in diagnostikdata aktiverar du loggning genom att ange var loggningsinformation lagras.
 

 
- [Så här samlar du in och analyserar loggdata för Azure Cognitive Search](search-monitor-logs.md) 

 
- [Samla in plattformsloggar och mått med Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Search:**

[!INCLUDE [Resource Policy for Microsoft.Search 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.search-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera kvarhållning av säkerhetslogglagring

**Vägledning:** Historiska data som matas in i diagnostikmått bevaras av Cognitive Search i 30 dagar som standard. För längre kvarhållning måste du aktivera inställningen som anger ett lagringsalternativ för ständiga loggade händelser och mått.
 

 
I Azure Monitor du kvarhållningsperioden för Log Analytics-arbetsytan enligt organisationens efterlevnadsregler. Använd Azure Storage-konton för långsiktig lagring och arkiveringslagring. 
 

 
- [Ändra kvarhållningsperioden för data i Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period) 

 
- [Så här konfigurerar du bevarandeprincip för Azure Storage-kontologgar](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#enable-logs)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning:** Analysera och övervaka loggar från din Cognitive Search för avvikande beteende. Använd Azure Monitor Log Analytics för att granska loggar och köra frågor på loggdata. Du kan också aktivera och ta med data till Azure Sentinel siem eller siem från tredje part. 

 
 
- [Så här samlar du in och analyserar loggdata för Cognitive Search](search-monitor-logs.md)
 
- [Visualisera sökloggdata i Power BI](search-monitor-logs-powerbi.md)
 

 
- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)
 

 
- [Läs mer om Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)
 

 
- [Så här utför du anpassade frågor i Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivera aviseringar för avvikande aktiviteter

**Vägledning:** Använd Security Center Log Analytics-arbetsyta för övervakning och avisering om avvikande aktivitet som finns i säkerhetsloggar och händelser. Du kan också aktivera och ta med data för att Azure Sentinel.
 

 
- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)
 

 
- [Hantera aviseringar i Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)
 

 
- [Så här varnar du om loggdata i Log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Upprätthålla en förteckning över administrativa konton

**Vägledning:** Med rollbaserad åtkomstkontroll i Azure (Azure RBAC) kan du hantera åtkomsten till Azure-resurser via rolltilldelningar. Du kan tilldela dessa roller till användare, grupper av tjänstens huvudnamn och hanterade identiteter. Det finns fördefinierade inbyggda roller för vissa resurser och dessa roller kan inventeras eller frågas via verktyg som Azure CLI, Azure PowerShell eller Azure-portalen.

Cognitive Search är associerade med behörigheter som stöder hanteringsuppgifter på servicenivå. De här rollerna beviljar inte åtkomst till tjänstslutpunkten. Åtkomst till åtgärder mot slutpunkten (till exempel indexhantering, indexpopulation och frågor om sökdata) använder API-nycklar för att autentisera begäran.

- [Ange roller för administrativ åtkomst till Azure Cognitive Search](search-security-rbac.md)

- [Skapa och hantera API-nycklar för en Azure Cognitive Search tjänst](search-security-api-keys.md)

- [Så här får du en katalogroll i Azure Active Directory (Azure AD) med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Så här hämtar du medlemmar i en katalogroll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Använd dedikerade administrativa konton

**Vägledning:** Cognitive Search har inte begreppet administratörskonton på lokal nivå eller Azure Active Directory (Azure AD) som kan användas för att hantera index och åtgärder. 

Använd de inbyggda Azure AD-rollerna som måste tilldelas explicit för hanteringsåtgärder. Anropa Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

- [Använda roller för administrativ åtkomst i Cognitive Search](search-security-rbac.md)

- [Så här får du en katalogroll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning:** Använda SSO-autentisering med Azure Active Directory (Azure AD) för att få åtkomst till söktjänstinformation för hanteringsåtgärder som stöds via Azure Resource Manager. 

Upprätta en process för att minska antalet identiteter och autentiseringsuppgifter genom att aktivera enkel inloggning för tjänsten med din organisations befintliga identiteter.

- [Förstå enkel inloggning med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använd multifaktorautentisering för Azure Active Directory åtkomst

**Vägledning:** Aktivera Azure Active Directory (Azure AD)s multifaktorautentiseringsfunktion och följ Security Center identitets- och åtkomstrekommendationerna.

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Övervaka identitet och åtkomst inom Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Använd dedikerade datorer (arbetsstationer för privilegierad åtkomst) för alla administrativa uppgifter

**Vägledning:** Använda en PAW (Privileged Access Workstation) med multifaktorautentisering som konfigurerats för att logga in på och få åtkomst till Azure-resurser.
 

 
- [Förstå säkra Och Azure-hanterade arbetsstationer](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
 

 
- [Så här aktiverar du Azure Active Directory multifaktorautentisering (Azure AD)](../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logga och varna om misstänkta aktiviteter från administrativa konton

**Vägledning:** Använd Azure Active Directory (Azure AD) säkerhetsrapporter och övervakning för att identifiera när misstänkt eller osäker aktivitet inträffar i miljön. Använd Security Center för att övervaka identitets- och åtkomstaktivitet.

- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här övervakar du användarnas identitets- och åtkomstrelaterade aktiviteter i Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning:** Använd Azure Active Directory (Azure AD) som centralt autentiserings- och auktoriseringssystem för hanteringsuppgifter på tjänstnivå i Azure Cognitive Search. Azure AD-identiteter beviljar inte åtkomst till söktjänstens slutpunkt.  Åtkomst till åtgärder som indexhantering, indexpopulation och frågor om sökdata är tillgängliga via API-nycklar.

- [Skapa och konfigurera en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Skapa och hantera API-nycklar för en Azure Cognitive Search tjänst](search-security-api-keys.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Granska och stämma av användaråtkomst regelbundet

**Vägledning:** Azure Active Directory (Azure AD) innehåller loggar som hjälper dig att identifiera inaktuella konton. Använd Azure AD:s identitets- och åtkomstgranskningar för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Användaråtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst. 

Granska diagnostikloggar från Cognitive Search efter aktivitet i söktjänstens slutpunkt, till exempel indexhantering, indexpopulation och frågor.

- [Förstå Azure AD-rapportering](/azure/active-directory/reports-monitoring/)

- [Så här använder du identitets- och åtkomstgranskningar i Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Övervaka åtgärder och aktivitet för Azure Cognitive Search](search-monitor-usage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning:** Åtkomst till Azure Active Directory inloggningsaktiviteter, gransknings- och riskhändelseloggkällor (Azure AD) gör att du kan integrera med val annat SIEM- eller övervakningsverktyg.

Effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka granskningsloggar och inloggningsloggar till en Log Analytics-arbetsyta. Konfigurera önskade aviseringar i Log Analytics-arbetsytan.

- [Integrera Azure-aktivitetsloggar med Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Avisering om kontoinloggningens beteendeavvikelse

**Vägledning:** Använd Azure Active Directory (Azure AD) Identity Protection för att konfigurera automatiserade svar på identifierade misstänkta åtgärder relaterade till användaridentiteter. Mata in data i Azure Sentinel för ytterligare undersökning efter behov.

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du riskprinciper för Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Upprätthålla en förteckning över känslig information

**Vägledning:** Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolera system som lagrar eller bearbetar känslig information

**Vägledning:** Implementera separata prenumerationer och/eller hanteringsgrupper för utveckling, testning och produktion. Resurserna ska avgränsas med virtuellt nätverk/undernät, taggas på rätt sätt och skyddas i en nätverkssäkerhetsgrupp eller Azure Firewall. Resurser som lagrar eller bearbetar känsliga data bör isoleras. Använd Private Link att konfigurera en privat slutpunkt för att Cognitive Search.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

- [Så här skapar du en privat slutpunkt för Cognitive Search](service-create-private-endpoint.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Övervaka och blockera obehörig överföring av känslig information

**Vägledning:** Använd en tredjepartslösning från en Azure Marketplace i nätverksnätverket för att övervaka obehörig överföring av känslig information och blockera sådana överföringar samtidigt som du varnar säkerhetspersonal.

Microsoft hanterar den underliggande plattformen och behandlar allt kundinnehåll som känsligt och skyddar mot förlust och exponering av kunddata. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och upprätthåller en uppsättning robusta dataskyddskontroller och funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Använd ett aktivt identifieringsverktyg för att identifiera känsliga data

**Vägledning:** Funktioner för dataidentifiering, klassificering och förlustskydd är ännu inte tillgängliga för Cognitive Search. Implementera en lösning från tredje part om det behövs för efterlevnadsändamål. 

Microsoft hanterar den underliggande plattformen och behandlar allt kundinnehåll som känsligt och skyddar mot förlust och exponering av kunddata. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och upprätthåller en uppsättning robusta dataskyddskontroller och funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Använd Azure RBAC för att hantera åtkomst till resurser

**Vägledning:** För tjänstadministration använder du rollbaserad åtkomstkontroll i Azure (Azure RBAC) för att hantera åtkomst till nycklar och konfiguration. För innehållsåtgärder, till exempel indexering och frågor, använder Cognitive Search nycklar i stället för en identitetsbaserad åtkomstkontrollmodell. Använd Azure RBAC för att styra åtkomsten till nycklar. 

 
- [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md)  
 
 
- [Använda roller för administrativ åtkomst till Cognitive Search](search-security-rbac.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Kryptera känslig information i vila

**Vägledning:** Cognitive Search krypterar automatiskt indexerat innehåll i vila med Microsoft-hanterade nycklar. Om du behöver mer skydd kan du komplettera standardkryptering med ett andra krypteringslager med hjälp av nycklar som du skapar och hanterar i Azure Key Vault.

- [Konfigurera kund hanterade nycklar för datakryptering i Azure Cognitive Search](search-security-manage-encryption-keys.md)

- [Förstå kryptering vid vila i Azure](../security/fundamentals/encryption-atrest.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisering om ändringar av viktiga Azure-resurser

**Vägledning:** Använd Azure Monitor med Azure-aktivitetsloggen för att skapa aviseringar för när ändringar sker i produktionsinstanser av Cognitive Search och andra kritiska eller relaterade resurser. 

 
- [Så här skapar du aviseringar för Azure-aktivitetslogghändelser](../azure-monitor/alerts/alerts-activity-log.md) 

 
- [Skapa aviseringar för Cognitive Search aktiviteter](search-monitor-logs.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Benchmark för Azure-säkerhet: Inventering och tillgångshantering.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Använd lösningen för automatisk tillgångsidentifiering

**Vägledning:** Använd Azure Resource Graph för att söka efter och identifiera alla resurser (till exempel beräkning, lagring, nätverk, portar, protokoll och så vidare) i dina prenumerationer.

Se till att du har rätt (läs) behörigheter i din klientorganisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

- [Skapa frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="62-maintain-asset-metadata"></a>6.2: Underhålla tillgångsmetadata

**Vägledning:** Tillämpa taggar på Azure-resurser med metadata för att organisera dem logiskt i en taxonomi.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ta bort obehöriga Azure-resurser

**Vägledning:** Använd taggning, hanteringsgrupper och separata prenumerationer när det är lämpligt för att organisera och spåra tillgångar. Stämma av inventeringen regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definiera och underhålla en förteckning över godkända Azure-resurser

**Vägledning:** Definiera en lista över godkända Azure-resurser relaterade till indexering och kompetensuppsättningsbearbetning i Cognitive Search.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Övervaka för ej godkända Azure-resurser

**Vägledning:** Vi rekommenderar att du definierar en inventering av Azure-resurser som har godkänts för användning enligt organisationens principer och standarder tidigare och sedan övervakar för ej godkända Azure-resurser med Azure Policy eller Azure Resource Graph.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="69-use-only-approved-azure-services"></a>6.9: Använd endast godkända Azure-tjänster

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

Använd Azure Resource Graph för att fråga efter eller identifiera resurser i dina prenumerationer. Se till att alla Azure-resurser som finns i miljön godkänns.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en viss resurstyp med Azure Policy](/azure/governance/policy/samples/)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning:** Använd villkorsstyrd åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "Blockera åtkomst" för Microsoft Azure-hanteringsappen.  

 
Kontrollera åtkomsten till de nycklar som används för att autentisera begäranden för alla andra åtgärder, särskilt de som rör innehåll med Cognitive Search.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security Benchmark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning:** Använd Azure Policy alias i namnområdet "Microsoft.Search" för att skapa anpassade principer för att granska eller framtvinga konfigurationen av Azure Cognitive Search resurser. Du kan också använda inbyggda Azure Policy för Cognitive Search tjänster som:

Aktivera granskningsloggning för Azure-resurser

Azure Resource Manager kan exportera mallen i JavaScript Object Notation (JSON), som bör granskas för att säkerställa att konfigurationerna uppfyller säkerhetskraven för din organisation.

Du kan också använda rekommendationerna från Azure Security Center som en säker konfigurationsbaslinje för dina Azure-resurser.

- [Azure Policy för regelefterlevnad för Azure Cognitive Search](security-controls-policy.md)

- [Visa tillgängliga Azure Policy alias](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Underhålla säkra Azure-resurskonfigurationer

**Vägledning:** Använd Azure Policy [neka] och [distribuera om det inte finns] för att tillämpa säkra inställningar i Cognitive Search tjänstresurser. 

Azure Resource Manager-mallar kan användas för att upprätthålla säkerhetskonfigurationen för dina Azure-resurser som krävs av din organisation. 

- [Förstå Azure Policy effekter](../governance/policy/concepts/effects.md)

- [Azure Policy för regelefterlevnad för Azure Cognitive Search](security-controls-policy.md)

- [Skapa och hantera principer för att använda kompatibilitet](../governance/policy/tutorials/create-and-manage.md)

- [översikt Azure Resource Manager mallar](../azure-resource-manager/templates/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Lagra konfiguration av Azure-resurser på ett säkert sätt

**Vägledning:** Om du använder anpassade Azure Policy bör du använda Azure DevOps eller Azure Repos för att lagra och hantera din kod på ett säkert sätt.

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Dokumentation om Azure Repos](/azure/devops/repos/index)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuera konfigurationshanteringsverktyg för Azure-resurser

**Vägledning:** Definiera och implementera standardsäkerhetskonfigurationer för dina Cognitive Search-tjänstresurser med hjälp av Azure Policy. 

Använd alias för att skapa anpassade principer för att granska eller framtvinga nätverkskonfigurationerna. Du kan också använda inbyggda principdefinitioner som är relaterade till dina specifika resurser. 

Dessutom kan du använda Azure Automation för att distribuera konfigurationsändringar och hantera principundantag. 

- [Azure Policy för regelefterlevnad för Azure Cognitive Search](security-controls-policy.md)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementera automatisk konfigurationsövervakning för Azure-resurser

**Vägledning:** Använd Security Center för att utföra baslinjesökningar av dina Cognitive Search-tjänstresurser.  Du kan också använda Azure Policy för att varna och granska dina resurskonfigurationer. 

- [Så här åtgärdar du rekommendationer i Azure Security Center](../security-center/security-center-remediate-recommendations.md)

- [Azure Policy för regelefterlevnad för Azure Cognitive Search](security-controls-policy.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning:** Använd Azure Managed Identities tillsammans med Azure Key Vault för att förenkla hemlighetshanteringen för dina molnprogram.

- [Använda hanterade identiteter för Azure-resurser](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Så här skapar du en Key Vault](../key-vault/general/quick-create-portal.md)

- [Så här tillhandahåller du Key Vault autentisering med en hanterad identitet](../key-vault/general/assign-access-policy-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Hantera identiteter säkert och automatiskt

**Vägledning:** Använda en hanterad Azure-identitet för att ge Cognitive Search åtkomst till andra Azure-tjänster som Key Vault- och indexerardatakällor med hjälp av en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Med hanterade identiteter kan du autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Azure Key Vault, utan några autentiseringsuppgifter i koden. 

- [Konfigurera en indexeraranslutning till en datakälla med hjälp av en hanterad identitet](search-howto-managed-identities-data-sources.md)

- [Konfigurera kund hanterade nycklar för datakryptering med hjälp av en hanterad identitet](search-security-manage-encryption-keys.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security Benchmark: Malware Defense.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Skanna filer i förväg som ska laddas upp till Icke-beräkningsbaserade Azure-resurser

**Vägledning:** Sök igenom allt innehåll som överförs till Azure-resurser som inte beräknas i förväg, till exempel Cognitive Search, Blob Storage, Azure SQL Database och så vidare. 

Det är ditt ansvar att försöka allt innehåll som laddas upp till icke-beräkningsbaserade Azure-resurser. Microsoft kan inte komma åt kunddata och kan därför inte utföra genomsökningar av kundinnehåll för din räkning.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3: Se till att program mot skadlig programvara och signaturer uppdateras

**Vägledning:** Gäller inte för Cognitive Search. Det tillåter inte att lösningar mot skadlig kod installeras på dess resurser. För den underliggande plattformen hanterar Microsoft uppdatering av program och signaturer mot skadlig kod.  

 
För alla beräkningsresurser som ägs av din organisation och används i söklösningen följer du rekommendationerna i Security Center, Compute Apps för att se till att alla slutpunkter är uppdaterade med de senaste &amp; signaturerna. För Linux använder du en tredjepartslösning mot skadlig kod.

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Prestandatest för Azure-säkerhet: Dataåterställning.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Säkerställ regelbundna automatiserade säkerhetskopieringar

**Vägledning:** Innehåll som lagras i en söktjänst kan inte säkerhetskopieras via Azure Backup eller någon annan inbyggd mekanism, men du kan återskapa ett index från programmets källkod och primära datakällor eller skapa ett anpassat verktyg för att hämta och lagra indexerat innehåll. 

 
- [GitHub-exempel på indexsäkerhetskopiering–återställning](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/index-backup-restore)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utför fullständiga systemsäkerhetskopior och säkerhetskopiera kund hanterade nycklar

**Vägledning:** Cognitive Search stöder för närvarande inte automatisk säkerhetskopiering av data i en söktjänst och måste säkerhetskopieras via en manuell process. Du kan också backa upp kund hanterade nycklar i Azure Key Vault.
 

- [Back up and restore an Azure Cognitive Search index](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Säkerhetskopiera Key Vault i Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Verifiera alla säkerhetskopior, inklusive kund hanterade nycklar

**Vägledning:** Cognitive Search stöder för närvarande inte automatisk säkerhetskopiering av data i en söktjänst och måste säkerhetskopieras och återställas via en manuell process. Utför regelbundet dataåterställning av innehåll som du har säkerhetskopierat manuellt för att säkerställa integriteten hos säkerhetskopieringsprocessen från slutet till slut.

- [Back up and restore an Azure Cognitive Search index](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Återställa Key Vault i Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Skydda säkerhetskopior och kund hanterade nycklar

**Vägledning:** Cognitive Search för närvarande inte stöder automatisk säkerhetskopiering av data i en söktjänst och måste säkerhetskopieras via en manuell process.  Du kan också backa upp kund hanterade nycklar i Azure Key Vault.  

 
Aktivera mjuk borttagning och rensningsskydd i Key Vault att skydda nycklar mot oavsiktlig eller skadlig borttagning. Om Azure Storage används för att lagra manuella säkerhetskopior aktiverar du mjuk borttagning för att spara och återställa data när blobar eller blobögonblicksbilder tas bort. 
 

 
- [Back up and restore an Azure Cognitive Search index](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)
 

 
- [Aktivera mjuk borttagning och rensningsskydd i Key Vault](../storage/blobs/soft-delete-blob-overview.md) 

- [Mjuk borttagning för Azure Blob Storage](../storage/blobs/soft-delete-blob-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Skapa en guide för incidentsvar

**Vägledning:** Utveckla en guide för incidenter för din organisation. Se till att det finns skriftliga planer för incidenthantering som definierar alla roller för personal samt faserna för incidenthantering och incidenthantering från identifiering till granskning efter incident.

- [Vägledning om hur du skapar en egen process för svar på säkerhetsincidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center är en incidents uppbyggnad](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Kunden kan också använda NIST:s guide för hantering av säkerhetsincidenter för att underlätta skapandet av en egen plan för incidenthantering](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Skapa en procedur för incidentbedömning och prioritering

**Vägledning:** Security Center tilldelar en allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som ska undersökas först. Allvarlighetsgraden baseras på hur säker Security Center är vid upptäckten eller det analytiskt använda för att utfärda aviseringen samt konfidensnivån för att det fanns skadlig avsikt bakom aktiviteten som ledde till aviseringen.

Markera dessutom prenumerationer med hjälp av taggar och skapa ett namngivningssystem för att identifiera och kategorisera Azure-resurser, särskilt de som bearbetar känsliga data. Det är ditt ansvar att prioritera åtgärder för aviseringar baserat på allvarligheten i de Azure-resurser och den miljö där incidenten inträffade.

- [Använda taggar för att organisera dina Azure-resurser](../azure-resource-manager/management/tag-resources.md)

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="103-test-security-response-procedures"></a>10.3: Testa procedurer för säkerhetssvar

**Vägledning:** Genomför övningar för att regelbundet testa systemets funktioner för incidenter. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [Se NIST:s publikation "Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities" (Guide till test-, tränings- och övningsprogram för IT-planer och IT-funktioner)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Ange kontaktuppgifter för säkerhetsincidenter och konfigurera aviseringsmeddelanden för säkerhetsincidenter

**Vägledning:** Kontaktinformation om säkerhetsincidenter används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en obehörig part. Granska incidenter i efterhand för att säkerställa att problemen är lösta.

- [Ställa in Azure Security Center säkerhetskontakt](../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Införliva säkerhetsaviseringar i systemet för incidenter

**Vägledning:** Exportera dina Security Center och rekommendationer med hjälp av funktionen Kontinuerlig export. Med löpande export kan du exportera aviseringar och rekommendationer manuellt eller kontinuerligt. Du kan använda anslutningsappen Security Center data för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du löpande export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisera svaret på säkerhetsaviseringar

**Vägledning:** Använd funktionen Arbetsflödesautomation i Azure Security Center att automatiskt utlösa svar via "Logic Apps" för säkerhetsaviseringar och rekommendationer.

- [Så här konfigurerar du arbetsflödesautomation och Logic Apps](../security-center/workflow-automation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [Prestandatest för Azure-säkerhet: Intrångstester och Red Team-övningar.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Genomför regelbundna intrångstester av dina Azure-resurser och se till att alla viktiga säkerhetsresultat åtgärdas

**Vägledning:** Följ Microsoft Cloud Penetration Testing Rules of Engagement för att säkerställa att dina intrångstester inte bryter mot Microsofts principer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft.

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
