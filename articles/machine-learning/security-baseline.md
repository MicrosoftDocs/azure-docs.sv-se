---
title: Azure-säkerhetsbaslinje för Azure Machine Learning
description: Säkerhetsbaslinjen Azure Machine Learning procedurvägledning och resurser för implementering av säkerhetsrekommendationerna som anges i Azure Security Benchmark.
author: msmbaldwin
ms.service: machine-learning
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6457624329d7bb5e367e9de5a1963884e11ad2e3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817007"
---
# <a name="azure-security-baseline-for-azure-machine-learning"></a>Azure-säkerhetsbaslinje för Azure Machine Learning

Den här säkerhetsbaslinjen tillämpar vägledning [från Azure Security Benchmark version 1.0](../security/benchmarks/overview-v1.md) till Microsoft Azure Machine Learning. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure.
Innehållet grupperas efter de säkerhetskontroller **som definierats** av Azure Security Benchmark och tillhörande vägledning som gäller för Azure Machine Learning. **Kontroller** som inte är Azure Machine Learning har undantagits.

 
Information om hur Azure Machine Learning mappning helt till Azure Security Benchmark finns i den fullständiga Azure Machine Learning [säkerhetsbaslinjemappningsfilen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Skydda Azure-resurser i virtuella nätverk

**Vägledning:** Azure Machine Learning förlitar sig på andra Azure-tjänster för beräkningsresurser. Beräkningsresurser (beräkningsmål) används för att träna och distribuera modeller. Du kan skapa dessa beräkningsmål i ett virtuellt nätverk. Du kan till exempel använda Azure Virtual Machine Learning-beräkningsinstans för att träna en modell och sedan distribuera modellen till Azure Kubernetes Service (AKS). Du kan skydda dina maskininlärningslivscykler genom att isolera Azure Machine Learning och härledningsjobb i ett virtuellt Azure-nätverk.

Azure Firewall kan användas för att styra åtkomsten till Azure Machine Learning arbetsyta och det offentliga Internet.

- [Översikt över isolering och sekretess för virtuella nätverk](how-to-network-security-overview.md)

- [Använd arbetsytan bakom Azure Firewall för Azure Machine Learning](how-to-access-azureml-behind-firewall.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Övervaka och logga konfiguration och trafik för virtuella nätverk, undernät och nätverkskort

**Vägledning:** Azure Machine Learning förlitar sig på andra Azure-tjänster för beräkningsresurser. Tilldela nätverkssäkerhetsgrupper till de nätverk som skapas som din Machine Learning distribution. 

Aktivera flödesloggar för nätverkssäkerhetsgruppen och skicka loggarna till ett Azure Storage konto för granskning. Du kan också skicka flödesloggarna till en Log Analytics-arbetsyta och sedan använda Trafikanalys för att ge insikter om trafikmönster i ditt Azure-moln. Vissa fördelar med Trafikanalys är möjligheten att visualisera nätverksaktivitet, identifiera hotpunkter och säkerhetshot, förstå trafikflödesmönster och hitta felkonfigurationer i nätverk.

- [Så här aktiverar du flödesloggar för nätverkssäkerhetsgrupp](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

- [Förstå nätverkssäkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="13-protect-critical-web-applications"></a>1.3: Skydda kritiska webbprogram

**Vägledning:** Du kan aktivera HTTPS för att skydda kommunikationen med webbtjänster som distribueras av Azure Machine Learning. Webbtjänster distribueras på Azure Kubernetes Services (AKS) eller Azure Container Instances (ACI) och skyddar de data som skickas av klienter. Du kan också använda privat IP med AKS för att begränsa bedömning, så att endast klienter bakom ett virtuellt nätverk kan komma åt webbtjänsten.

- [Använda TLS för att skydda en webbtjänst via Azure Machine Learning](how-to-secure-web-service.md)

- [Översikt över isolering och sekretess för virtuella nätverk](how-to-network-security-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

**Vägledning:** Aktivera DDoS Protection Standard på de virtuella nätverk som är associerade med din Machine Learning-instans för att skydda mot distribuerade DDoS-attacker (Distributed Denial-of-Service). Använd Azure Security Center integrerad hotidentifiering för att identifiera kommunikation med kända skadliga eller oanvända Internet-IP-adresser.

Distribuera Azure Firewall vid var och en av organisationens nätverksgränser med hotinformationsbaserad filtrering aktiverad och konfigurerad för att "varna och neka" för skadlig nätverkstrafik.

- [Så här konfigurerar du DDoS-skydd](../ddos-protection/manage-ddos-protection.md)

- [Använd arbetsytan bakom Azure Firewall för Azure Machine Learning](how-to-access-azureml-behind-firewall.md)

- [Mer information om Azure Security Center hotidentifiering](../security-center/azure-defender.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="15-record-network-packets"></a>1.5: Registrera nätverkspaket

**Vägledning:** För alla virtuella datorer med rätt tillägg installerat i dina Azure Machine Learning-tjänster kan du aktivera Network Watcher paketinfångst för att undersöka avvikande aktiviteter. 

- [Så här skapar du en Network Watcher instans](../network-watcher/network-watcher-create.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuera nätverksbaserade system för intrångsidentifiering/intrångsskydd (IDS/IPS)

**Vägledning:** Distribuera valfri brandväggslösning vid var och en av organisationens nätverksgränser för att identifiera och/eller blockera skadlig trafik.

Välj ett erbjudande från Azure Marketplace som stöder IDS/IPS-funktioner med funktioner för nyttolastgranskning.  När kontroll av nyttolast inte är ett Azure Firewall kan hotinformation användas. Azure Firewall filtrering baserad på hotinformation används för att varna om och/eller blockera trafik till och från kända skadliga IP-adresser och domäner. IP-adresserna och domänerna hämtas från Microsoft Threat Intelligence-flödet.

- [Så här distribuerar du Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Så här konfigurerar du aviseringar med Azure Firewall](../firewall/threat-intel.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Hantera trafik till webbprogram

**Vägledning:** Ej tillämpligt; den här rekommendationen är avsedd för webbprogram som körs Azure App Service eller beräkningsresurser.

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexitet och administrativa kostnader för nätverkssäkerhetsregler

**Vägledning:** För resurser som behöver åtkomst till ditt Azure Machine Learning-konto använder du Virtual Network-tjänsttaggar för att definiera nätverksåtkomstkontroller för nätverkssäkerhetsgrupper eller Azure Firewall. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttaggnamnet (till exempel AzureMachineLearning) i rätt käll- eller målfält för en regel kan du tillåta eller neka trafik för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras.

Azure Machine Learning Service dokumenterar en lista över tjänsttaggar för dess beräkningsmål i ett virtuellt nätverk som hjälper till att minimera komplexiteten kan du använda den som riktlinjer i din nätverkshantering.

- [Mer information om hur du använder tjänsttaggar](../virtual-network/service-tags-overview.md)

- [Översikt över isolering och sekretess för virtuella nätverk](how-to-network-security-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Underhålla standardsäkerhetskonfigurationer för nätverksenheter

**Vägledning:** Definiera och implementera standardsäkerhetskonfigurationer för nätverksresurser som är associerade Azure Machine Learning nätverksnamnrymder med Azure Policy. Använd Azure Policy alias i namnrymderna "Microsoft.MachineLearning" och "Microsoft.Network" för att skapa anpassade principer för att granska eller framtvinga nätverkskonfigurationen för Machine Learning namnområden. 

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentera trafikkonfigurationsregler

**Vägledning:** Använd taggar för nätverksresurser som är associerade med Azure Machine Learning-distributionen för att organisera dem logiskt enligt en taxonomi.

För en resurs i Azure Machine Learning nätverk som stöder fältet Beskrivning kan du använda det för att dokumentera reglerna som tillåter trafik till/från ett nätverk.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

**Vägledning:** Använd Azure-aktivitetsloggen för att övervaka nätverksresurskonfigurationer och identifiera ändringar för nätverksresurser relaterade till Azure Machine Learning. Skapa aviseringar inom Azure Monitor som utlöses när ändringar av kritiska nätverksresurser sker.

- [Så här visar och hämtar du Händelser i Azure-aktivitetsloggen](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Skapa aviseringar i Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [Prestandatest för Azure-säkerhet: Loggning och övervakning.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera central hantering av säkerhetsloggar

**Vägledning:** Mata in loggar via Azure Monitor för att aggregera säkerhetsdata som genereras av Azure Machine Learning. I Azure Monitor använder du Log Analytics-arbetsytor för att köra frågor mot och utföra analyser och använda Azure Storage-konton för långsiktig lagring och arkiveringslagring. Du kan också aktivera och ta med data för att Azure Sentinel eller en säkerhetsincident- och händelsehantering (SIEM) från tredje part.

- [Så här konfigurerar du diagnostikloggar för Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#configuration)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägledning:** Aktivera diagnostikinställningar för Azure-resurser för åtkomst till gransknings-, säkerhets- och diagnostikloggar. Aktivitetsloggar, som är automatiskt tillgängliga, inkluderar händelsekälla, datum, användare, tidsstämpel, källadresser, måladresser och andra användbara element.

Du kan också korrelera Machine Learning av säkerhets- och efterlevnadsändamål.

- [Samla in plattformsloggar och mått med Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)

- [Förstå loggning och olika loggtyper i Azure](/azure/azure-monitor/platform/platform-logs-overview)

- [Aktivera loggning i Azure Machine Learning](how-to-log-view-metrics.md)

- [Övervakning Azure Machine Learning](monitor-azure-machine-learning.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Samla in säkerhetsloggar från operativsystem

**Vägledning:** Om beräkningsresursen ägs av Microsoft ansvarar Microsoft för att samla in och övervaka den. 

Azure Machine Learning har varierande stöd för olika beräkningsresurser och även dina egna beräkningsresurser. För alla beräkningsresurser som ägs av din organisation använder Azure Security Center för att övervaka operativsystemet. 

- [Så här samlar du in interna värdloggar för virtuella Azure-datorer med Azure Monitor](/azure/azure-monitor/learn/quick-collect-azurevm)

- [Förstå Azure Security Center datainsamling](../security-center/security-center-enable-data-collection.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera kvarhållning av säkerhetslogglagring

**Vägledning:** I Azure Monitor anger du loggens kvarhållningsperiod för Log Analytics-arbetsytor som är associerade med dina Azure Machine Learning-instanser enligt organisationens efterlevnadsregler.

- [Så här anger du parametrar för loggbevarande](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning:** Analysera och övervaka loggar för avvikande beteende och granska regelbundet resultaten från dina Azure Machine Learning. Använd Azure Monitor och en Log Analytics-arbetsyta för att granska loggar och köra frågor på loggdata.

Du kan också aktivera och ta med data till Azure Sentinel eller en SIEM från tredje part. 

- [Så här utför du frågor för Azure Machine Learning i Log Analytics-arbetsytor](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#analyzing-log-data)

- [Aktivera loggning i Azure Machine Learning](how-to-log-view-metrics.md)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Komma igång med Log Analytics-frågor](../azure-monitor/logs/log-analytics-tutorial.md)

- [Så här utför du anpassade frågor i Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivera aviseringar för avvikande aktiviteter

**Vägledning:** I Azure Monitor konfigurerar du loggar relaterade till Azure Machine Learning i aktivitetsloggen och Machine Learning-diagnostikinställningar för att skicka loggar till en Log Analytics-arbetsyta som ska efterfrågas eller till ett lagringskonto för långsiktig arkiveringslagring. Använd Log Analytics-arbetsytan för att skapa aviseringar för avvikande aktivitet som finns i säkerhetsloggar och händelser.

Du kan också aktivera och ta med data för att Azure Sentinel.

- [Mer information om Azure Machine Learning aviseringar](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#alerts)

- [Så här varnar du om loggdata för Log Analytics-arbetsytor](/azure/azure-monitor/learn/tutorial-response)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralisera loggning mot skadlig kod

**Vägledning:** Om beräkningsresursen ägs av Microsoft ansvarar Microsoft för distributionen av program mot skadlig programvara Azure Machine Learning Service.

Azure Machine Learning har varierande stöd för olika beräkningsresurser och även dina egna beräkningsresurser. För beräkningsresurser som ägs av din organisation aktiverar du insamling av program mot skadlig programvara för Microsoft Antimalware för Azure Cloud Services och Virtual Machines.

- [Så här konfigurerar du Microsoft Antimalware för molntjänster](/powershell/module/servicemanagement/azure.service/set-azurevmmicrosoftantimalwareextension)

- [Förstå Microsoft Antimalware](../security/fundamentals/antimalware.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivera DNS-frågeloggning

**Vägledning:** Inte tillämpligt; Azure Machine Learning inte bearbetar eller producerar DNS-relaterade loggar.

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivera granskningsloggning på kommandoraden

**Vägledning:** Azure Machine Learning har varierande stöd för olika beräkningsresurser och även dina egna beräkningsresurser. För beräkningsresurser som ägs av din organisation använder du Azure Security Center för att aktivera övervakning av säkerhetshändelseloggar för virtuella Azure-datorer. Azure Security Center etablerar Log Analytics-agenten på alla virtuella Azure-datorer som stöds och eventuella nya som skapas om automatisk etablering är aktiverat. Eller så kan du installera agenten manuellt. Agenten aktiverar processgenereringshändelsen 4688 och kommandoradsfältet i händelse 4688. Nya processer som skapas på den virtuella datorn registreras av händelseloggen och övervakas Security Center av identifieringstjänster.

- [Datainsamling i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Upprätthålla en förteckning över administrativa konton

**Vägledning:** Du kan använda fliken Identitets- och åtkomsthantering för en resurs i Azure Portal för att konfigurera rollbaserad åtkomstkontroll i Azure (Azure RBAC) och underhålla inventeringen på Azure Machine Learning resurser. Rollerna tillämpas på användare, grupper, tjänstens huvudnamn och hanterade identiteter i Active Directory. Du kan använda inbyggda roller eller anpassade roller för enskilda användare och grupper.

Azure Machine Learning har inbyggda roller för vanliga hanteringsscenarier i Azure Machine Learning. En person som har en profil i Azure Active Directory (Azure AD) kan tilldela dessa roller till användare, grupper, tjänstens huvudnamn eller hanterade identiteter för att bevilja eller neka åtkomst till resurser och åtgärder på Azure Machine Learning resurser.

Du kan också använda Azure AD PowerShell-modulen för att köra adhoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

- [Förstå rollbaserad åtkomstkontroll i Azure i Azure Machine Learning](how-to-assign-roles.md)

- [Så här får du en katalogroll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändra standardlösenord om tillämpligt

**Vägledning:** Åtkomsthantering till Machine Learning-resurser styrs via Azure Active Directory (Azure AD). Azure AD har inte begreppet standardlösenord.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Använd dedikerade administrativa konton

**Vägledning:** Azure Machine Learning levereras med tre standardroller när en ny arbetsyta skapas, vilket skapar standardprocedurer för användning av ägarkonton.

Du kan också aktivera just-in-time-åtkomst till administrativa konton med hjälp av Azure Active Directory (Azure AD) Privileged Identity Management och Azure Resource Manager.

- [Läs mer om Machine Learning standardroller](https://docs.microsoft.com/azure/machine-learning/how-to-assign-roles#default-roles)

- [Läs mer om Privileged Identity Management](/azure/active-directory/privileged-identity-management/index)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning:** Machine Learning är integrerad med Azure Active Directory (Azure AD) använder du enkel inloggning med Azure AD i stället för att konfigurera enskilda fristående autentiseringsuppgifter per tjänst. Använd Azure Security Center rekommendationer för identitet och åtkomst.

- [Förstå enkel inloggning med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använd multifaktorautentisering för Azure Active Directory åtkomst

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

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logga och varna om misstänkta aktiviteter från administrativa konton

**Vägledning:** Använd Azure Active Directory (Azure AD) och övervakning för att identifiera när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure Security Center för att övervaka identitets- och åtkomstaktivitet.

- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här övervakar du användarnas identitets- och åtkomstrelaterade aktiviteter i Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Hantera Azure-resurser endast från godkända platser

**Vägledning:** Använd Azure Active Directory (Azure AD) namngivna platser för att endast tillåta åtkomst från specifika logiska grupper av IP-adressintervall eller länder/regioner.

- [Så här konfigurerar du azure AD-namngivna platser](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning:** Använd Azure Active Directory (Azure AD) som centralt system för autentisering och auktorisering. Azure AD skyddar data med hjälp av stark kryptering för data i vila och under överföring. Azure AD saltar, hashar och lagrar användarautentiseringsuppgifter på ett säkert sätt.
 
Rollåtkomst kan vara begränsad till flera nivåer i Azure. Till Machine Learning kan roller hanteras på arbetsytenivå, till exempel om du har ägaråtkomst till en arbetsyta kanske inte har ägaråtkomst till resursgruppen som innehåller arbetsytan. Detta ger mer detaljerade åtkomstkontroller för att avgränsa roller inom samma resursgrupp. 

- [Hantera åtkomst till en Azure Machine Learning-arbetsyta](how-to-assign-roles.md) 
 
- [Skapa och konfigurera en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Granska och stämma av användaråtkomst regelbundet

**Vägledning:** Azure Active Directory (Azure AD) innehåller loggar som hjälper dig att identifiera inaktuella konton. Använd dessutom Azure AD-identitet och åtkomstgranskningar för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Användaråtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

Använd Azure AD Privileged Identity Management (PIM) för generering av loggar och aviseringar när misstänkt eller osäker aktivitet inträffar i miljön.

- [Förstå Azure AD-rapportering](/azure/active-directory/reports-monitoring)

- [Så här använder du identitets- och åtkomstgranskningar i Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Distribuera Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning:** Du har åtkomst till Azure Active Directory inloggningsaktiviteter, gransknings- och riskhändelseloggkällor (Azure AD), vilket gör att du kan integrera med valbara SIEM-/övervakningsverktyg.

Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka granskningsloggar och inloggningsloggar till en Log Analytics-arbetsyta. Du kan konfigurera önskade aviseringar i Log Analytics-arbetsytan.

- [Integrera Azure-aktivitetsloggar med Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Avisering om kontoinloggningens beteendeavvikelse

**Vägledning:** Använd Azure Active Directory (Azure AD) Identity Protection-funktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder relaterade till användaridentiteter. Du kan också mata in data i Azure Sentinel för ytterligare undersökning.

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du riskprinciper för Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ge Microsoft åtkomst till relevanta kunddata under supportscenarier

**Vägledning:** Ej tillämpligt; Azure Machine Learning Service stöder inte customer lockbox.

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Upprätthålla en förteckning över känslig information

**Vägledning:** Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information.
 
- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolera system som lagrar eller bearbetar känslig information

**Vägledning:** Implementera isolering med separata prenumerationer och hanteringsgrupper för enskilda säkerhetsdomäner, till exempel miljötyp och känslighetsnivå för data. Du kan begränsa åtkomstnivån till dina Azure-resurser som dina program och företagsmiljöer kräver. Du kan styra åtkomsten till Azure-resurser via Azure RBAC.
 
- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

 
- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Övervaka och blockera obehörig överföring av känslig information

**Vägledning:** Använd en tredjepartslösning från en Azure Marketplace i nätverksnätverket för att övervaka obehörig överföring av känslig information och blockera sådana överföringar samtidigt som du varnar säkerhetspersonal. 

När det gäller den underliggande plattformen, som hanteras av Microsoft, behandlar Microsoft allt kundinnehåll som känsligt och skyddar det mot förlust och exponering av kunddata. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och upprätthåller en uppsättning robusta dataskyddskontroller och -funktioner. 

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Kryptera all känslig information under överföring

**Vägledning:** Webbtjänster som distribueras via Azure Machine Learning stöder endast TLS version 1.2 som tillämpar datakryptering under överföring.

- [Använda TLS för att skydda en webbtjänst via Azure Machine Learning](how-to-secure-web-service.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Använd ett aktivt identifieringsverktyg för att identifiera känsliga data

**Vägledning:** Funktioner för dataidentifiering, klassificering och förlustskydd är ännu inte tillgängliga för Azure Machine Learning. Implementera en lösning från tredje part om det behövs för efterlevnadsändamål.

För den underliggande plattformen, som hanteras av Microsoft, behandlar Microsoft allt kundinnehåll som känsligt och går mycket långt för att skydda mot förlust och exponering av kunddata. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och upprätthåller en uppsättning robusta dataskyddskontroller och funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Använd Azure RBAC för att hantera åtkomst till resurser

**Vägledning:** Azure Machine Learning stöder användning Azure Active Directory (Azure AD) för att auktorisera begäranden till Machine Learning resurser. Med Azure AD kan du använda rollbaserad åtkomstkontroll i Azure (Azure RBAC) för att bevilja behörigheter till ett säkerhetsobjekt, som kan vara en användare eller ett huvudnamn för programtjänsten.

- [Hantera åtkomst till en Azure Machine Learning-arbetsyta](how-to-assign-roles.md)

- [Använda Azure RBAC för Kubernetes-auktorisering](../aks/manage-azure-rbac.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Kryptera känslig information i vila

**Vägledning:** Azure Machine Learning lagrar ögonblicksbilder, utdata och loggar i Azure Blob Storage-kontot som är kopplat till Azure Machine Learning och din prenumeration. Alla data som lagras i Azure Blob Storage krypteras i vila med Microsoft-hanterade nycklar. Du kan också kryptera data som lagras i Azure Blob Storage med dina egna nycklar Machine Learning tjänsten. 

- [Azure Machine Learning datakryptering i vila](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#encryption-at-rest)

- [Förstå kryptering vid vila i Azure](../security/fundamentals/encryption-atrest.md)

- [Så här konfigurerar du kund hanterade krypteringsnycklar](../storage/common/customer-managed-keys-configure-key-vault.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisering om ändringar av viktiga Azure-resurser

**Vägledning:** Använd Azure Monitor med Azure-aktivitetsloggen för att skapa aviseringar för när ändringar sker i produktionsinstanser av Azure Machine Learning och andra kritiska eller relaterade resurser.

- [Så här skapar du aviseringar för Azure-aktivitetslogghändelser](/azure/azure-monitor/platform/alerts-activity-log)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Köra automatiserade sårbarhetsgenomsökningsverktyg

**Vägledning:** Om beräkningsresursen ägs av Microsoft ansvarar Microsoft för sårbarhetshantering av Azure Machine Learning Service. 

Azure Machine Learning har olika stöd för olika beräkningsresurser och även dina egna beräkningsresurser. För beräkningsresurser som ägs av din organisation följer du rekommendationerna från Azure Security Center för att utföra sårbarhetsbedömningar på dina virtuella Azure-datorer, containeravbildningar och SQL-servrar.

- [Så här implementerar du Azure Security Center rekommendationer för sårbarhetsbedömning](../security-center/deploy-vulnerability-assessment-vm.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Distribuera automatisk lösning för korrigeringshantering av operativsystem

**Vägledning:** Om beräkningsresursen ägs av Microsoft ansvarar Microsoft för korrigeringshantering av Azure Machine Learning Service. 

Azure Machine Learning har olika stöd för olika beräkningsresurser och även dina egna beräkningsresurser. För alla beräkningsresurser som ägs av din organisation använder du Azure Automation Uppdateringshantering för att säkerställa att de senaste säkerhetsuppdateringarna installeras på dina virtuella Windows- och Linux-datorer. För virtuella Windows-datorer ser du Windows Update har aktiverats och ställts in på att uppdateras automatiskt.

- [Så här konfigurerar Uppdateringshantering för virtuella datorer i Azure](../automation/update-management/overview.md)

- [Förstå Azures säkerhetsprinciper som övervakas av Security Center](../security-center/policy-reference.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Distribuera en automatiserad korrigeringshanteringslösning för programvarutitlar från tredje part

**Vägledning:** Azure Machine Learning har olika stöd för olika beräkningsresurser och även dina egna beräkningsresurser. För beräkningsresurser som ägs av din organisation använder du en lösning för hantering av korrigeringar från tredje part. Kunder som redan Konfigurationshanteraren i sin miljö kan också använda System Center Updates Publisher, så att de kan publicera anpassade uppdateringar i Windows Server Update Service. Detta gör Uppdateringshantering att korrigera datorer som använder Konfigurationshanteraren som deras uppdateringslager med programvara från tredje part.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Jämför tillbaka-till-tillbaka-sårbarhetsgenomsökningar

**Vägledning:** Azure Machine Learning har varierande stöd för olika beräkningsresurser och även dina egna beräkningsresurser. För beräkningsresurser som ägs av din organisation följer du rekommendationerna från Azure Security Center för att utföra sårbarhetsbedömningar på dina virtuella Azure-datorer, containeravbildningar och SQL-servrar. Exportera genomsökningsresultat med konsekventa intervall och jämför resultaten med tidigare genomsökningar för att verifiera att sårbarheter har åtgärdats. När du använder rekommendationer för sårbarhetshantering Azure Security Center kan du gå till den valda lösningens portal för att visa historiska genomsökningsdata.

- [Så här implementerar du Azure Security Center rekommendationer för sårbarhetsbedömning](../security-center/deploy-vulnerability-assessment-vm.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Använd en riskklassificeringsprocess för att prioritera åtgärder för identifierade säkerhetsrisker

**Vägledning:** Ej tillämpligt; den här riktlinjen är avsedd för beräkningsresurser.

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security Benchmark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Använd automatisk tillgångsidentifiering

**Vägledning:** Använd Azure Resource Graph för att fråga efter och identifiera resurser (till exempel beräkning, lagring, nätverk, portar och protokoll osv.) i dina prenumerationer. Se till att du har rätt (läs) behörigheter i din klientorganisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via Azure Resource Graph Explorer rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser i framtiden.

- [Skapa frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="62-maintain-asset-metadata"></a>6.2: Underhålla tillgångsmetadata

**Vägledning:** Tillämpa taggar på Azure-resurser och lägg till metadata för att organisera dem logiskt i en taxonomi.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ta bort obehöriga Azure-resurser

**Vägledning:** Använd taggning, hanteringsgrupper och separata prenumerationer när det är lämpligt för att organisera och spåra tillgångar. Stämma av inventeringen regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

- [ Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [ Så här skapar du hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)
 
- [ Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definiera och underhålla en förteckning över godkända Azure-resurser

**Vägledning:** Skapa en förteckning över godkända Azure-resurser och godkänd programvara för beräkningsresurser enligt organisationens behov.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Övervaka för ej godkända Azure-resurser

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:

* Otillåtna resurstyper
* Tillåtna resurstyper

Dessutom kan du använda Azure Resource Graph för att fråga/identifiera resurser i prenumerationerna.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Övervaka för program som inte är godkända i beräkningsresurser

**Vägledning:** Azure Machine Learning har varierande stöd för olika beräkningsresurser och även dina egna beräkningsresurser. För beräkningsresurser som ägs av din organisation använder du Azure Automation Ändringsspårning och inventering för att automatisera insamlingen av inventeringsinformation från dina virtuella Windows- och Linux-datorer. Programnamn, version, utgivare och uppdateringstid är tillgängliga från Azure Portal. Om du vill hämta programinstallationsdatumet och annan information aktiverar du diagnostik på gästnivå och dirigerar Windows-händelseloggarna till Log Analytics-arbetsytan.

- [Så här aktiverar du Azure Automation inventeringssamling för en virtuell dator](../automation/automation-tutorial-installed-software.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Ta bort ej godkända Azure-resurser och program

**Vägledning:** Azure Machine Learning har varierande stöd för olika beräkningsresurser och även dina egna beräkningsresurser. För beräkningsresurser som ägs av din organisation använder Azure Security Center filintegritetsövervakning (FIM) för att identifiera all programvara som är installerad på virtuella datorer. Ett annat alternativ som kan användas i stället för eller tillsammans med FIM är Azure Automation Ändringsspårning och inventering att samla in inventering från dina virtuella Linux- och Windows-datorer. 

Du kan implementera en egen process för att ta bort obehörig programvara. Du kan också använda en lösning från tredje part för att identifiera ej godkänd programvara.

Ta bort Azure-resurser när de inte längre behövs.

- [Så här använder du övervakning av filintegritet](../security-center/security-center-file-integrity-monitoring.md)

- [Förstå Azure Automation Ändringsspårning och inventering](../automation/change-tracking/overview.md)

- [Så här aktiverar du inventering av virtuella Azure-datorer](../automation/automation-tutorial-installed-software.md)

- [Azure-resursgrupp och resursborttagning](../azure-resource-manager/management/delete-resource-group.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="68-use-only-approved-applications"></a>6.8: Använd endast godkända program

**Vägledning:** Azure Machine Learning har olika stöd för olika beräkningsresurser och även dina egna beräkningsresurser. För beräkningsresurser som ägs av din organisation använder du Azure Security Center anpassningsbara programkontroller för att säkerställa att endast auktoriserad programvara körs och att all obehörig programvara blockeras från att köras på Azure Virtual Machines.

- [Så här använder du Azure Security Center anpassningsbara programkontroller](../security-center/security-center-adaptive-application.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="69-use-only-approved-azure-services"></a>6.9: Använd endast godkända Azure-tjänster

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:

* Otillåtna resurstyper
* Tillåtna resurstyper

Dessutom kan du använda Azure Resource Graph för att fråga efter och identifiera resurser i prenumerationerna.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Upprätthålla en förteckning över godkända programvarutitlar

**Vägledning:** Azure Machine Learning har olika stöd för olika beräkningsresurser och även dina egna beräkningsresurser. För alla beräkningsresurser som ägs av din organisation använder du Azure Security Center anpassningsbara programkontroller för att ange vilka filtyper som en regel kan eller inte gäller för.

Implementera en lösning från tredje part om anpassningsbara programkontroller inte uppfyller kravet.

- [Så här använder du Azure Security Center anpassningsbara programkontroller](../security-center/security-center-adaptive-application.md)

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning:** Använd villkorlig Azure Active Directory (Azure AD) för att begränsa användarnas möjlighet att interagera med Azure Resources Manager genom att konfigurera "Blockera åtkomst" för appen "Microsoft Azure Management".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resources Manager](../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: Begränsa användarnas möjlighet att köra skript i beräkningsresurser

**Vägledning:** Azure Machine Learning har varierande stöd för olika beräkningsresurser och även dina egna beräkningsresurser. För beräkningsresurser som ägs av din organisation kan du, beroende på typen av skript, använda operativsystemspecifika konfigurationer eller resurser från tredje part för att begränsa användarnas möjlighet att köra skript i Azure-beräkningsresurser. Du kan också använda Azure Security Center anpassningsbara programkontroller för att säkerställa att endast auktoriserad programvara körs och att all obehörig programvara blockeras från att köras på Azure Virtual Machines.

- [Så här styr du PowerShell-skriptkörning i Windows-miljöer](/powershell/module/microsoft.powershell.security/set-executionpolicy)

- [Så här använder du Azure Security Center anpassningsbara programkontroller](../security-center/security-center-adaptive-application.md)

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fysiskt eller logiskt avsegrera högriskprogram

**Vägledning:** Ej tillämpligt; den här rekommendationen är avsedd för webbprogram som körs Azure App Service eller beräkningsresurser.

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security Benchmark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning:** Definiera och implementera standardsäkerhetskonfigurationer för Azure Machine Learning Service med Azure Policy. Använd Azure Policy alias i namnområdet "Microsoft.MachineLearning" för att skapa anpassade principer för att granska eller framtvinga konfigurationen av dina Azure Machine Learning tjänster.

Azure Resource Manager har möjlighet att exportera mallen i JavaScript Object Notation (JSON), som bör granskas för att säkerställa att konfigurationerna uppfyller säkerhetskraven för din organisation.

Du kan också använda rekommendationerna från Azure Security Center som en säker konfigurationsbaslinje för dina Azure-resurser.

Azure Machine Learning har fullständigt stöd för Git-lagringsplatsen för spårningsarbete. du kan klona lagringsplatsen direkt till ditt delade arbetsytefilsystem, använda Git på din lokala arbetsstation och se till att säkra konfigurationer gäller för kodresurser som en del av din Machine Learning miljö.

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias)

- [Självstudie: Skapa och hantera principer för att framtvinga efterlevnad](../governance/policy/tutorials/create-and-manage.md)

- [Export av en enskild resurs och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Säkerhetsrekommendationer – en referensguide](../security-center/recommendations-reference.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Upprätta säkra operativsystemkonfigurationer

**Vägledning:** Om beräkningsresursen ägs av Microsoft ansvarar Microsoft för operativsystems säkra konfigurationer av Azure Machine Learning Service.

Azure Machine Learning har olika stöd för olika beräkningsresurser och även dina egna beräkningsresurser. För beräkningsresurser som ägs av din organisation kan du använda Azure Security Center rekommendationer för att upprätthålla säkerhetskonfigurationer på alla beräkningsresurser.  Dessutom kan du använda anpassade operativsystemavbildningar eller Azure Automation-tillståndskonfiguration för att upprätta säkerhetskonfigurationen för det operativsystem som krävs av din organisation.

- [Övervaka Azure Security Center rekommendationer](../security-center/security-center-recommendations.md)

- [Säkerhetsrekommendationer – en referensguide](../security-center/recommendations-reference.md)

- [Azure Automation State Configuration översikt](../automation/automation-dsc-overview.md)

- [Ladda upp en virtuell hårddisk och använda den för att skapa nya virtuella Windows-datorer i Azure](../virtual-machines/windows/upload-generalized-managed.md)

- [Skapa en virtuell Linux-dator från en anpassad disk med Azure CLI](../virtual-machines/linux/upload-vhd.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Underhålla säkra Azure-resurskonfigurationer

**Vägledning:** Använd Azure Policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar för alla dina Azure-resurser. Dessutom kan du använda Azure Resource Manager för att upprätthålla säkerhetskonfigurationen för dina Azure-resurser som krävs av din organisation. 
 
- [Förstå Azure Policy effekter](../governance/policy/concepts/effects.md)
 
- [Skapa och hantera principer för att använda kompatibilitet](../governance/policy/tutorials/create-and-manage.md)
 
- [ översikt Azure Resource Manager mallar](../azure-resource-manager/templates/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Upprätthåll säkra operativsystemkonfigurationer

**Vägledning:** Om beräkningsresursen ägs av Microsoft ansvarar Microsoft för operativsystems säkra konfigurationer av Azure Machine Learning Service.

Azure Machine Learning har varierande stöd för olika beräkningsresurser och även dina egna beräkningsresurser. För beräkningsresurser som ägs av din organisation följer du rekommendationerna från Azure Security Center att utföra sårbarhetsbedömningar på dina Azure-beräkningsresurser.  Dessutom kan du använda Azure Resource Manager, anpassade operativsystemavbildningar eller Azure Automation State Configuration för att upprätthålla säkerhetskonfigurationen för det operativsystem som krävs av din organisation.   Microsofts mallar för virtuella datorer i kombination med Azure Automation State Configuration kan hjälpa till att uppfylla och upprätthålla säkerhetskraven. 

Observera att Azure Marketplace virtuella datorer Avbildningar som publicerats av Microsoft hanteras och underhålls av Microsoft. 

- [Så här implementerar du Azure Security Center rekommendationer för sårbarhetsbedömning](../security-center/deploy-vulnerability-assessment-vm.md)

- [Så här skapar du en virtuell Azure-dator från en ARM-mall](../virtual-machines/windows/ps-template.md)

- [Azure Automation State Configuration översikt](../automation/automation-dsc-overview.md)

- [Skapa en virtuell Windows-dator i Azure Portal](../virtual-machines/windows/quick-create-portal.md)

- [Information om hur du laddar ned VM-mallen](/previous-versions/azure/virtual-machines/windows/download-template)

- [Exempelskript för att överföra en virtuell hårddisk till Azure och skapa en ny virtuell dator](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Lagra konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning:** Om du använder anpassade Azure Policy för dina Machine Learning eller relaterade resurser använder du Azure Repos för att lagra och hantera din kod på ett säkert sätt.

Azure Machine Learning har fullständigt stöd för Git-lagringsplatsen för spårningsarbete. du kan klona lagringsplatsen direkt till ditt delade arbetsytefilsystem, använda Git på din lokala arbetsstation och se till att säkra konfigurationer gäller för kodresurser som en del av din Machine Learning miljö.

- [Lagra kod i Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Dokumentation om Azure Repos](/azure/devops/repos/)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Lagra anpassade operativsystemavbildningar på ett säkert sätt

**Vägledning:** Azure Machine Learning har olika stöd för olika beräkningsresurser och även dina egna beräkningsresurser. För beräkningsresurser som ägs av din organisation använder du rollbaserad åtkomstkontroll i Azure (Azure RBAC) för att säkerställa att endast behöriga användare kan komma åt dina anpassade avbildningar. Använd en Azure Shared Image Gallery du kan dela dina avbildningar till olika användare, tjänsthuvudnamn eller Azure Active Directory-grupper (Azure AD) i din organisation. Lagra containeravbildningar i Azure Container Registry och använd Azure RBAC för att se till att endast behöriga användare har åtkomst.

- [Förstå Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Förstå Azure RBAC för Container Registry](../container-registry/container-registry-roles.md)

- [Så här konfigurerar du Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Shared Image Gallery översikt](../virtual-machines/shared-image-galleries.md)

- [Använda Azure RBAC för Kubernetes-auktorisering](../aks/manage-azure-rbac.md)

**Ansvar:** Ej tillämpligt

**Azure Security Center övervakning**: Ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuera konfigurationshanteringsverktyg för Azure-resurser

**Vägledning:** Använd Azure Policy alias i namnområdet "Microsoft.MachineLearning" för att skapa anpassade principer för att varna, granska och framtvinga systemkonfigurationer. Utveckla dessutom en process och pipeline för hantering av principundantag.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här använder du alias](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Distribuera konfigurationshanteringsverktyg för operativsystem

**Vägledning:** Om beräkningsresursen ägs av Microsoft ansvarar Microsoft för säker konfigurationsdistribution av Azure Machine Learning Service.

Azure Machine Learning har varierande stöd för olika beräkningsresurser och även dina egna beräkningsresurser. För beräkningsresurser som ägs av din organisation använder du Azure Automation State Configuration för Desired State Configuration-noder (DSC) i alla molndatacenter eller lokala datacenter. Du kan enkelt publicera datorer, tilldela dem deklarativa konfigurationer och visa rapporter som visar varje dators kompatibilitet med det önskade tillstånd som du har angett. 

- [Så här aktiverar du Azure Automation State Configuration](../automation/automation-dsc-onboarding.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementera automatisk konfigurationsövervakning för Azure-resurser

**Vägledning:** Använd Azure Security Center för att utföra baslinjesökningar för dina Azure-resurser. Du kan också använda Azure Policy för att varna och granska Azure-resurskonfigurationer.
 
 
 
- [ Så här åtgärdar du rekommendationer i Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementera automatisk konfigurationsövervakning för operativsystem

**Vägledning:** Om beräkningsresursen ägs av Microsoft ansvarar Microsoft för automatisk säker konfigurationsövervakning av Azure Machine Learning Service.

Azure Machine Learning har varierande stöd för olika beräkningsresurser och även dina egna beräkningsresurser. För beräkningsresurser som ägs av din organisation använder du Azure Security Center Compute Apps och följer rekommendationerna &amp; för virtuella datorer, servrar och containrar.

- [Förstå rekommendationer för Azure Security Center-container](../security-center/container-security.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning:** Använd hanterade identiteter tillsammans med Azure Key Vault för att förenkla hemlighetshanteringen för dina molnprogram.

Azure Machine Learning har stöd för kryptering av datalager med kundhanterade nycklar måste du hantera nyckelrotering och återkallande enligt organisationens säkerhets- och efterlevnadskrav. 

Använd Azure Key Vault att skicka hemligheter till fjärrkörningar på ett säkert sätt i stället för att rensa text i dina träningsskript.

- [Azure Machine Learning kund hanterade nycklar](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-blob-storage)

- [Använda hemligheter för autentiseringsuppgifter i Azure Machine Learning träningskörningar](how-to-use-secrets-in-runs.md)

- [Använda hanterade identiteter för Azure-resurser](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Så här skapar du en Key Vault](../key-vault/general/quick-create-portal.md)

- [Så här autentiserar du till Key Vault](../key-vault/general/authentication.md)

- [Tilldela en Key Vault åtkomstprincip](../key-vault/general/assign-access-policy-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Hantera identiteter säkert och automatiskt

**Vägledning:** Azure Machine Learning har stöd för både inbyggda roller och möjligheten att skapa anpassade roller. Använd hanterade identiteter för att ge Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Med hanterade identiteter kan du autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan några autentiseringsuppgifter i koden.

- [Hantera åtkomst till en Azure Machine Learning-arbetsyta](how-to-assign-roles.md)

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

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: Använd centralt hanterad programvara mot skadlig programvara

**Vägledning:** Microsofts program mot skadlig kod är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure Machine Learning), men den körs inte på kundinnehåll.

Azure Machine Learning har varierande stöd för olika beräkningsresurser och även dina egna beräkningsresurser. För beräkningsresurser som ägs av din organisation kan du använda Microsoft Antimalware azure för att kontinuerligt övervaka och skydda dina resurser. För Linux använder du en tredjepartslösning mot skadlig programvara. Använd även Azure Security Center hotidentifiering för datatjänster för att identifiera skadlig kod som laddats upp till lagringskonton.

- [Så här konfigurerar Microsoft Antimalware för Azure](../security/fundamentals/antimalware.md)

- [Skydd mot hot i Azure Security Center](../security-center/azure-defender.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Skanna filer i förväg som ska överföras till Icke-beräkningsbaserade Azure-resurser

**Vägledning:** Microsofts program mot skadlig kod är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure Machine Learning), men den körs inte på kundinnehåll.

Det är ditt ansvar att skanna allt innehåll som laddas upp till icke-beräkningsbaserade Azure-resurser i förväg. Microsoft kan inte komma åt kunddata och kan därför inte utföra genomsökningar av kundinnehåll för din räkning.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3: Se till att program mot skadlig programvara och signaturer uppdateras

**Vägledning:** Microsofts program mot skadlig kod är aktiverat och underhålls för den underliggande värden som stöder Azure-tjänster (till exempel Azure Machine Learning), men den körs inte på kundinnehåll.

Azure Machine Learning har varierande stöd för olika beräkningsresurser och även dina egna beräkningsresurser. För alla beräkningsresurser som ägs av din organisation följer du rekommendationerna i Azure Security Center, Compute Apps för att se till att alla slutpunkter är uppdaterade med de &amp; senaste signaturerna. För Linux använder du en tredjepartslösning mot skadlig programvara.

- [Så här distribuerar du Microsoft Antimalware för Azure](../security/fundamentals/antimalware.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Prestandatest för Azure-säkerhet: Dataåterställning.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Säkerställ regelbundna automatiserade säkerhetskopieringar

**Vägledning:** Dataåterställningshantering i Machine Learning-tjänsten är via datahantering i anslutna datalager. Se till att följa upp riktlinjerna för dataåterställning för anslutna butiker för att rekommendationer för data ska kunna återställas enligt kundens organisationsprinciper.

- [Återställa filer från säkerhetskopiering av virtuella Azure-datorer](../backup/backup-azure-restore-files-from-vm.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utför fullständiga systemsäkerhetskopior och säkerhetskopiera kund hanterade nycklar

**Vägledning:** Säkerhetskopiering av data i Machine Learning-tjänsten är via datahantering på anslutna datalager. Aktivera Azure Backup virtuella datorer och konfigurera önskad frekvens och kvarhållningsperioder. Back up customer-managed keys in Azure Key Vault.

- [Återställa filer från säkerhetskopiering av virtuella Azure-datorer](../backup/backup-azure-restore-files-from-vm.md)

- [Återställa Key Vault i Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Verifiera alla säkerhetskopior, inklusive kund hanterade nycklar

**Vägledning:** Verifiering av datasäkerhetskopiering Machine Learning tjänsten är via datahantering på anslutna datalager. Utför regelbundet dataåterställning av innehåll i Azure Backup. Se till att du kan återställa säkerhetskopierade kund hanterade nycklar.

- [Återställa filer från en säkerhetskopiering av en virtuell Azure-dator](../backup/backup-azure-restore-files-from-vm.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Skydda säkerhetskopior och kund hanterade nycklar

**Vägledning:** Vid lokal säkerhetskopiering tillhandahålls kryptering i vila med hjälp av lösenfrasen som du anger när du säkerhetskopierar till Azure. Använd rollbaserad åtkomstkontroll i Azure (Azure RBAC) för att skydda säkerhetskopior och kund hanterade nycklar. 

Aktivera mjuk borttagning och rensningsskydd i Key Vault att skydda nycklar mot oavsiktlig eller skadlig borttagning. Om Azure Storage används för att lagra säkerhetskopior aktiverar du mjuk borttagning för att spara och återställa data när blobar eller blobögonblicksbilder tas bort.

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

- [Aktivera mjuk borttagning och rensningsskydd i Key Vault](../storage/blobs/soft-delete-blob-overview.md)

- [Mjuk borttagning för Azure Blob Storage](../storage/blobs/soft-delete-blob-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Skapa en incidentsvarsguide

**Vägledning:** Utveckla en guide för incidentsvar för din organisation. Se till att det finns skriftliga incidenthanteringsplaner som definierar alla roller för personal samt faserna för incidenthantering och hantering från identifiering till granskning efter incident. 

- [Vägledning om hur du skapar en egen process för svar på säkerhetsincidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center är en incidents uppbyggnad](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Använd NIST:s guide för hantering av incidenter vid datorsäkerhet för att skapa en egen plan för incidenthantering](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Skapa en procedur för bedömning och prioritering av incidenter

**Vägledning:** Azure Security Center tilldelar en allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som ska undersökas först. Allvarlighetsgraden baseras på hur säker Security Center är vid upptäckten eller det analytiskt använda för att utfärda aviseringen samt konfidensnivån för att det fanns skadlig avsikt bakom aktiviteten som ledde till aviseringen.

Markera dessutom prenumerationer med hjälp av taggar och skapa ett namngivningssystem för att identifiera och kategorisera Azure-resurser, särskilt de som bearbetar känsliga data. Det är ditt ansvar att prioritera åtgärder för aviseringar baserat på allvarligheten i de Azure-resurser och den miljö där incidenten inträffade.

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

**Vägledning:** Exportera dina Azure Security Center och rekommendationer med hjälp av funktionen för kontinuerlig export för att identifiera risker för Azure-resurser. Med löpande export kan du exportera aviseringar och rekommendationer manuellt eller kontinuerligt. Du kan använda anslutningsappen Azure Security Center data för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du löpande export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisera svaret på säkerhetsaviseringar

**Vägledning:** Använd funktionen för arbetsflödesautomatisering Azure Security Center att automatiskt utlösa svar på säkerhetsaviseringar och rekommendationer för att skydda dina Azure-resurser.

- [Så här konfigurerar du arbetsflödesautomation i Security Center](../security-center/workflow-automation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [Azure Security Benchmark: Penetration Tests (Intrångstester) och Red Team Exercises (Red Team-övningar).](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Genomför regelbundna intrångstester av dina Azure-resurser och se till att alla viktiga säkerhetsresultat åtgärdas

**Vägledning:** Följ Microsoft Cloud Penetration Testing Rules of Engagement för att säkerställa att intrångstesterna inte bryter mot Microsofts principer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft.

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
