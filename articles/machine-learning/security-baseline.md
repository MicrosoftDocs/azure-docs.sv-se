---
title: Azures säkerhets bas linje för Azure Machine Learning
description: Azure Machine Learning säkerhets bas linje ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 08aa69db90901447297e06cd111b6b0174e6637a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575087"
---
# <a name="azure-security-baseline-for-azure-machine-learning"></a>Azures säkerhets bas linje för Azure Machine Learning

Azures säkerhets bas linje för Microsoft Azure Machine Learning innehåller rekommendationer som hjälper dig att förbättra säkerhets position för din distribution. Bas linjen för den här tjänsten hämtas från [Azures prestandatest version 1,0](../security/benchmarks/overview.md), som ger rekommendationer om hur du kan skydda dina moln lösningar i Azure med våra bästa praxis rikt linjer. Mer information finns i [Översikt över Azure Security-bas linjer](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets principen för Azure-säkerhet: nätverks säkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: Azure Machine Learning använder andra Azure-tjänster för beräknings resurser. Beräknings resurser (beräknings mål) används för att träna och distribuera modeller. Du kan skapa dessa beräknings mål i ett virtuellt nätverk. Du kan till exempel använda Azure Virtual Machine Learning Compute instance för att träna en modell och sedan distribuera modellen till Azure Kubernetes service (AKS). Du kan skydda dina Machine Learning-livscykler genom att isolera Azure Machine Learning utbildning och jobb härlednings jobb i ett virtuellt Azure-nätverk.

Azure-brandväggen kan användas för att styra åtkomsten till din Azure Machine Learning-arbetsyta och det offentliga Internet.

- [Översikt över virtuella nätverks isolering och sekretess](how-to-network-security-overview.md)

- [Använd arbets ytan bakom Azure-brandväggen för Azure Machine Learning](how-to-access-azureml-behind-firewall.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverkskort

**Vägledning**: Azure Machine Learning använder andra Azure-tjänster för beräknings resurser. Tilldela nätverks säkerhets grupper till de nätverk som skapas som din Machine Learning-distribution. 

Aktivera flödes loggar för nätverks säkerhets grupper och skicka loggarna till ett Azure Storage konto för granskning. Du kan också skicka flödes loggarna till en Log Analytics arbets yta och sedan använda Trafikanalys för att ge insikter om trafik mönster i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet, identifiera frekventa fläckar och säkerhetshot, förstå trafikflödes mönster och hitta nätverks-felkonfigurationer.

- [Så här aktiverar du flödes loggar för nätverks säkerhets grupper](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

- [Förstå nätverks säkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: du kan aktivera https för att skydda kommunikationen med webb tjänster som distribueras av Azure Machine Learning. Webb tjänster distribueras på Azure Kubernetes Services (AKS) eller Azure Container Instances (ACI) och skyddar data som skickats av klienter. Du kan också använda privat IP med AKS för att begränsa poängen, så att endast klienter bakom ett virtuellt nätverk kan komma åt webb tjänsten.

- [Använda TLS för att skydda en webbtjänst via Azure Machine Learning](how-to-secure-web-service.md)

- [Översikt över virtuella nätverks isolering och sekretess](how-to-network-security-overview.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Aktivera DDoS Protection standard på de virtuella nätverk som är kopplade till din Machine Learning instans för att skydda mot distribuerade DDoS-attacker (Denial-of-Service). Använd Azure Security Center integrerad hot identifiering för att identifiera kommunikation med kända skadliga eller oanvända Internet-IP-adresser.

Distribuera Azure-brandväggen på var och en av organisationens nätverks gränser med hot intelligens-baserad filtrering aktive rad och konfigurerad för "varning och neka" för skadlig nätverks trafik.

- [Så här konfigurerar du DDoS-skydd](../ddos-protection/manage-ddos-protection.md)

- [Använd arbets ytan bakom Azure-brandväggen för Azure Machine Learning](how-to-access-azureml-behind-firewall.md)

- [Mer information om Azure Security Center hot identifiering](../security-center/azure-defender.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets"></a>1,5: registrera nätverks paket

**Vägledning**: för virtuella datorer med rätt tillägg installerat i dina Azure Machine Learning-tjänster kan du aktivera Network Watcher paket fångst för att undersöka avvikande aktiviteter. 

- [Så här skapar du en Network Watcher-instans](../network-watcher/network-watcher-create.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera nätverksbaserade intrångs identifiering/system för skydd mot intrång (ID/IP-adresser)

**Vägledning**: distribuera den brand Väggs lösning som du väljer för var och en av organisationens nätverks gränser för att identifiera och/eller blockera skadlig trafik.

Välj ett erbjudande från Azure Marketplace som stöder ID/IP-funktioner med funktioner för nytto Last kontroll.  När nytto lasts granskning inte är ett krav kan du använda Azure Firewall Threat intelligence. Azure Firewall Threat Intelligence-baserad filtrering används för att varna vid och/eller blockera trafik till och från kända skadliga IP-adresser och domäner. IP-adresserna och domänerna hämtas från Microsoft Threat Intelligence-flödet.

- [Så här distribuerar du Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)

- [Konfigurera aviseringar med Azure-brandväggen](../firewall/threat-intel.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: för resurser som behöver åtkomst till ditt Azure Machine Learning-konto använder du Virtual Network Service-taggar för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper eller Azure-brandvägg. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet (till exempel AzureMachineLearning) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

Azure Machine Learning tjänst dokument en lista över service märken för beräknings målen i ett virtuellt nätverk som bidrar till att minimera komplexiteten kan du använda det som rikt linjer i din nätverks hantering.

- [Mer information om att använda service märken](../virtual-network/service-tags-overview.md)

- [Översikt över virtuella nätverks isolering och sekretess](how-to-network-security-overview.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för nätverks resurser som är kopplade till dina Azure Machine Learning namnrum med Azure policy. Använd Azure Policy alias i namn områdena "Microsoft. MachineLearning" och "Microsoft. Network" om du vill skapa anpassade principer för granskning eller tillämpa nätverks konfigurationen för dina Machine Learning-namnområden. 

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för nätverks resurser som är associerade med din Azure Machine Learning-distribution för att logiskt organisera dem enligt en taxonomi.

För en resurs i ditt Azure Machine Learning virtuella nätverk som har stöd för fältet Beskrivning använder du det för att dokumentera de regler som tillåter trafik till/från ett nätverk.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar för nätverks resurser som är relaterade till Azure Machine Learning. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks resurser sker.

- [Visa och hämta Azure aktivitets logg händelser](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets benchmark för Azure: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

**Vägledning**: Microsoft hanterar den tids källa som används för Azure-resurser, till exempel Azure Machine Learning för tidsstämplar i loggarna.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: mata in loggar via Azure Monitor för att samla in säkerhets data som genereras av Azure Machine Learning. I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring. Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en säkerhets incident och händelse hantering från tredje part (SIEM).

- [Så här konfigurerar du diagnostikloggar för Azure Machine Learning](monitor-azure-machine-learning.md#configuration)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Aktivera diagnostiska inställningar på Azure-resurser för åtkomst till gransknings-, säkerhets-och diagnostikloggar. Aktivitets loggar, som är automatiskt tillgängliga, innehåller händelse källa, datum, användare, tidsstämpel, käll adresser, mål adresser och andra användbara element.

Du kan också korrelera Machine Learning tjänstens åtgärds loggar i syfte att övervaka säkerhet och efterlevnad.

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Förstå loggning och olika logg typer i Azure](../azure-monitor/essentials/platform-logs-overview.md)

- [Aktivera loggning i Azure Machine Learning](./how-to-track-experiments.md)

- [Övervaknings Azure Machine Learning](monitor-azure-machine-learning.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

**Vägledning**: om beräknings resursen ägs av Microsoft ansvarar Microsoft för att samla in och övervaka den. 

Azure Machine Learning har varierande stöd för olika beräknings resurser och till och med dina egna beräknings resurser. Använd Azure Security Center för att övervaka operativ systemet för alla beräknings resurser som ägs av din organisation. 

- [Så här samlar du in interna värd loggar för virtuella Azure-datorer med Azure Monitor](../azure-monitor/vm/quick-collect-azurevm.md)

- [Förstå Azure Security Center insamling av data](../security-center/security-center-enable-data-collection.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: i Azure Monitor anger du logg kvarhållningsperioden för Log Analytics arbets ytor som är kopplade till dina Azure Machine Learning instanser enligt organisationens regler för efterlevnad.

- [Ange parametrar för logg bevarande](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: analysera och övervaka loggar för avvikande beteende och granska resultaten regelbundet från Azure Machine Learning. Använd Azure Monitor och en Log Analytics arbets yta för att granska loggar och köra frågor på loggdata.

Du kan också aktivera och inaktivera data till Azure Sentinel eller en SIEM från tredje part. 

- [Så här kör du frågor för Azure Machine Learning i Log Analytics arbets ytor](monitor-azure-machine-learning.md#analyzing-log-data)

- [Aktivera loggning i Azure Machine Learning](./how-to-track-experiments.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Komma igång med Log Analytics frågor](../azure-monitor/logs/log-analytics-tutorial.md)

- [Så här utför du anpassade frågor i Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: i Azure Monitor konfigurerar du loggar som är relaterade till Azure Machine Learning i aktivitets loggen och Machine Learning diagnostikinställningar för att skicka loggar till en Log Analytics arbets yta som ska frågas eller till ett lagrings konto för långsiktig lagring. Använd Log Analytics arbets yta för att skapa aviseringar för avvikande aktivitet i säkerhets loggar och händelser.

Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel.

- [Mer information om Azure Machine Learning-aviseringar](monitor-azure-machine-learning.md#alerts)

- [Avisering om logg data för Log Analytics arbets ytor](../azure-monitor/alerts/tutorial-response.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: om beräknings resursen ägs av Microsoft ansvarar Microsoft för distribution av program mot skadlig kod för Azure Machine Learning-tjänsten. 

Azure Machine Learning har varierande stöd för olika beräknings resurser och till och med dina egna beräknings resurser. För beräknings resurser som ägs av din organisation aktiverar du händelse insamling för program mot skadlig kod för Microsoft Antimalware för Azure Cloud Services och Virtual Machines.

- [Så här konfigurerar du Microsoft Antimalware för en virtuell dator](/powershell/module/servicemanagement/azure.service/set-azurevmmicrosoftantimalwareextension)

- [Så här konfigurerar du Microsoft Antimalware-tillägget för Cloud Services](/powershell/module/servicemanagement/azure.service/set-azureserviceantimalwareextension?view=azuresmps-4.0.0)

- [Förstå Microsoft Antimalware](../security/fundamentals/antimalware.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Delad

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: ej tillämpligt; Azure Machine Learning bearbetar eller skapar inte DNS-relaterade loggar.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

**Vägledning**: Azure Machine Learning har varierande stöd för olika beräknings resurser och till och med dina egna beräknings resurser. För beräknings resurser som ägs av din organisation kan du använda Azure Security Center för att aktivera övervakning av säkerhets händelse logg för virtuella Azure-datorer. Azure Security Center etablerar Log Analytics-agenten på alla virtuella Azure-datorer som stöds och eventuella nya som skapas om automatisk etablering är aktiverat. Eller så kan du installera agenten manuellt. Agenten gör det möjligt för processen att skapa händelse 4688 och fältet kommandorad i händelse 4688. Nya processer som skapas på den virtuella datorn registreras av händelse logg och övervakas av Security Centers identifierings tjänster.

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azures säkerhets benchmark: identitets-och åtkomst kontroll](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: du kan använda fliken identitets-och åtkomst hantering för en resurs i Azure Portal för att konfigurera rollbaserad åtkomst kontroll i Azure (Azure RBAC) och underhålla inventeringen på Azure Machine Learning resurser. Rollerna tillämpas på användare, grupper, tjänstens huvud namn och hanterade identiteter i Active Directory. Du kan använda inbyggda roller eller anpassade roller för enskilda användare och grupper.

Azure Machine Learning innehåller inbyggda roller för vanliga hanterings scenarier i Azure Machine Learning. En person som har en profil i Azure Active Directory (Azure AD) kan tilldela roller till användare, grupper, tjänstens huvud namn eller hanterade identiteter för att bevilja eller neka åtkomst till resurser och åtgärder på Azure Machine Learning resurser.

Du kan också använda Azure AD PowerShell-modulen för att utföra adhoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

- [Lär dig mer om rollbaserad åtkomst kontroll i Azure i Azure Machine Learning](how-to-assign-roles.md)

- [Så här hämtar du en katalog roll i Azure Active Directory med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: åtkomst hantering till Machine Learning resurser styrs via Azure Active Directory (Azure AD). Azure AD har inte begreppet standard lösen ord.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: Azure Machine Learning levereras med tre standard roller när en ny arbets yta skapas, vilket skapar standard procedurer kring användningen av ägar konton.

Du kan också aktivera en just-in-Time-åtkomst till administrativa konton genom att använda Azure AD Privileged Identity Management och Azure Resource Manager. 

- [Läs mer Machine Learning standard roller](how-to-assign-roles.md#default-roles)

- [Läs mer om Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: Machine Learning är integrerat med Azure Active Directory använder du Azure Active Directory SSO i stället för att konfigurera enskilda fristående autentiseringsuppgifter per tjänst. Använd Azure Security Center identitets-och åtkomst rekommendationer.  

- [Förstå SSO med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory baserad åtkomst

**Vägledning**: aktivera Azure Active Directory Multi-Factor Authentication och följ Azure Security Center identitets-och åtkomst rekommendationer.

- [Aktivera MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd en säker, Azure-hanterad arbets Station (kallas även en privilegie rad åtkomst arbets Station eller Paw) för administrativa uppgifter som kräver förhöjda privilegier.

- [Förstå säkra, Azure-hanterade arbets stationer](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Så här aktiverar du Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: Använd Azure Active Directory säkerhets rapporter och övervakning för att identifiera när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure Security Center för att övervaka identitets-och åtkomst aktiviteter.

- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här övervakar du användarnas identitets- och åtkomstrelaterade aktiviteter i Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Använd namngivna platser i Azure AD för att endast tillåta åtkomst från särskilda logiska grupperingar av IP-adressintervall eller länder/regioner.
 
 
 
- [Så här konfigurerar du Azure AD-namngivna platser](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: använda Azure Active Directory (Azure AD) som central autentiserings-och auktoriserings system. Azure AD skyddar data med stark kryptering för data i vila och under överföring. Azure AD innehåller även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.
 
Roll åtkomsten kan begränsas till flera nivåer i Azure. För Machine Learning kan roller hanteras på arbets ytan, till exempel om du har ägar åtkomst till en arbets yta kanske inte har ägar åtkomst till den resurs grupp som innehåller arbets ytan. Detta ger mer detaljerade åtkomst kontroller för att separera roller inom samma resurs grupp. 

- [Hantera åtkomst till en Azure Machine Learning-arbetsyta](how-to-assign-roles.md) 
 
- [Skapa och konfigurera en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure AD innehåller loggar som hjälper till att identifiera inaktuella konton. Dessutom kan du använda Azure AD-identitets-och åtkomst granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användar åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst. 
 
Använd Azure Active Directory (Azure AD) Privileged Identity Management (PIM) för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön.

- [Förstå Azure AD repor ting](../active-directory/reports-monitoring/index.yml)

- [Så här använder du identitets- och åtkomstgranskningar i Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Distribuera Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: du har åtkomst till inloggnings aktiviteter, gransknings-och risk händelse logg källor i Azure AD, vilket gör att du kan integrera med alla Siem/övervaknings verktyg.

Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka gransknings loggar och inloggnings loggar till en Log Analytics-arbetsyta. Du kan konfigurera önskade aviseringar i Log Analytics arbets ytan.
 
 
- [Så här integrerar du Azure-aktivitets loggar med Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: avisering om beteende för beteende för konto inloggning

**Vägledning**: Använd Azure AD Identity Protection funktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter. Du kan också mata in data i Azure Sentinel för ytterligare undersökning.
 
- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)
 
- [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)
 
- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: ej tillämpligt; Azure Machine Learning tjänsten stöder inte kundsäkerhet.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security benchmark: Data Protection](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information.
 
- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera isolering med separata prenumerationer och hanterings grupper för enskilda säkerhets domäner, till exempel miljö typ och data känslighets nivå. Du kan begränsa åtkomst nivån till dina Azure-resurser som dina program och företags miljöer kräver. Du kan styra åtkomsten till Azure-resurser via Azure RBAC.
 
- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du hanterings grupper](../governance/management-groups/create-management-group-portal.md)
 
- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: Använd en lösning från tredje part från Azure Marketplace i nätverks omkretser för att övervaka obehörig överföring av känslig information och blockera sådana överföringar samtidigt som du varnar information om informations säkerhet. 

När det gäller den underliggande plattformen, som hanteras av Microsoft, behandlar Microsoft allt kundinnehåll som känsligt och skyddar det mot förlust och exponering av kunddata. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner. 

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: webb tjänster som distribueras via Azure Machine Learning endast stöd för TLS version 1,2 som tillämpar data kryptering under överföring.

- [Använda TLS för att skydda en webbtjänst via Azure Machine Learning](how-to-secure-web-service.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure Machine Learning. Implementera en lösning från tredje part om det behövs för efterlevnad.

För den underliggande plattformen, som hanteras av Microsoft, behandlar Microsoft allt kund innehåll som känsligt och ger fantastiska längder för att skydda mot förlust och exponering av kund information. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Använd Azure RBAC för att hantera åtkomst till resurser

**Vägledning**: Azure Machine Learning stöder användning av Azure Active Directory (Azure AD) för att auktorisera begär anden till Machine Learning resurser. Med Azure AD kan du använda rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att ge behörighet till ett säkerhets objekt, som kan vara en användare eller ett huvud namn för en program tjänst.

- [Hantera åtkomst till en Azure Machine Learning-arbetsyta](how-to-assign-roles.md)
- [Använd Azure RBAC för Kubernetes-auktorisering](../aks/manage-azure-rbac.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

Microsoft hanterar den underliggande infrastrukturen för Machine Learning och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: Azure Machine Learning lagrar ögonblicks bilder, utdata och loggar i Azure Blob Storage-kontot som är knutet till Azure Machine Learning arbets ytan och din prenumeration. Alla data som lagras i Azure Blob Storage krypteras i vila med Microsoft-hanterade nycklar. Du kan också kryptera data som lagras i Azure Blob Storage med dina egna nycklar i Machine Learning-tjänsten. 

- [Azure Machine Learning data kryptering i vila](concept-enterprise-security.md#encryption-at-rest)

- [Förstå kryptering vid vila i Azure](../security/fundamentals/encryption-atrest.md)

- [Konfigurera kundens hanterade krypterings nycklar](../storage/common/customer-managed-keys-configure-key-vault.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i produktions instanser av Azure Machine Learning och andra kritiska eller relaterade resurser.

- [Så här skapar du aviseringar för Azure aktivitets logg händelser](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security benchmark: sårbarhet Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: om beräknings resursen ägs av Microsoft ansvarar Microsoft för sårbarhets hantering av Azure Machine learnings tjänsten. 

Azure Machine Learning har varierande stöd för olika beräknings resurser och till och med dina egna beräknings resurser. För beräknings resurser som ägs av din organisation följer du rekommendationerna från Azure Security Center för att utföra sårbarhets bedömningar på dina virtuella Azure-datorer, behållar avbildningar och SQL-servrar.

- [Så här implementerar du rekommendationer för Azure Security Center sårbarhets bedömning](../security-center/deploy-vulnerability-assessment-vm.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Delad

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: om beräknings resursen ägs av Microsoft, ansvarar Microsoft för korrigerings hantering av Azure Machine learnings tjänsten. 

Azure Machine Learning har varierande stöd för olika beräknings resurser och till och med dina egna beräknings resurser. För alla beräknings resurser som ägs av din organisation använder du Azure Automation Uppdateringshantering för att se till att de senaste säkerhets uppdateringarna är installerade på dina virtuella Windows-och Linux-datorer. För virtuella Windows-datorer kontrollerar du att Windows Update har Aktiver ATS och kon figurer ATS för automatisk uppdatering.

- [Så här konfigurerar du Uppdateringshantering för virtuella datorer i Azure](../automation/update-management/overview.md)

- [Förstå Azures säkerhets principer som övervakas av Security Center](../security-center/policy-reference.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Delad

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Distribuera en automatiserad korrigerings hanterings lösning för program varu titlar från tredje part

**Vägledning**: Azure Machine Learning har varierande stöd för olika beräknings resurser och till och med dina egna beräknings resurser. Använd en lösning för korrigerings hantering från tredje part för beräknings resurser som ägs av din organisation. Kunder som redan använder Configuration Manager i sin miljö kan också använda System Center Updates Publisher, så att de kan publicera anpassade uppdateringar i Windows Server Update-tjänsten. På så sätt kan Uppdateringshantering korrigera datorer som använder Configuration Manager som uppdaterings lagrings plats med program vara från tredje part.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: Azure Machine Learning har varierande stöd för olika beräknings resurser och till och med dina egna beräknings resurser. För beräknings resurser som ägs av din organisation följer du rekommendationer från Azure Security Center för att utföra sårbarhets bedömningar på dina virtuella Azure-datorer, behållar avbildningar och SQL-servrar. Exportera genomsöknings resultat med jämna mellanrum och jämför resultaten med tidigare genomsökningar för att verifiera att sårbarheter har åtgärd ATS. När du använder rekommendationer för sårbarhets hantering som föreslås av Azure Security Center kan du pivotera till den valda lösningens Portal för att visa historiska skannings data.

- [Så här implementerar du rekommendationer för Azure Security Center sårbarhets bedömning](../security-center/deploy-vulnerability-assessment-vm.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga efter och identifiera resurser (t. ex. data bearbetning, lagring, nätverk, portar och protokoll osv.) i dina prenumerationer.  Se till att du har rätt (Läs) behörigheter i din klient organisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via Azure Resource Graph Explorer, rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser som går framåt.

- [Skapa frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: använda taggar för Azure-resurser, lägga till metadata för logisk sortering efter en taxonomi.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer om det behövs för att organisera och spåra till gångar. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.
 
 
 
- [ Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)
 
 
 
- [ Så här skapar du hanterings grupper](../governance/management-groups/create-management-group-portal.md)
 
 
 
- [ Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla en inventering av godkända Azure-resurser

**Vägledning**: skapa en inventering av godkända Azure-resurser och godkänd program vara för beräknings resurser enligt organisationens behov.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

* Otillåtna resurstyper
* Tillåtna resurstyper

Använd dessutom Azure Resource Graph för att fråga/identifiera resurser i prenumerationerna.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: Azure Machine Learning har varierande stöd för olika beräknings resurser och till och med dina egna beräknings resurser. Använd Azure Automation Ändringsspårning och inventering för att automatisera insamling av inventerings information från dina virtuella Windows-och Linux-datorer för beräknings resurser som ägs av din organisation. Program varu namn, version, utgivare och uppdaterings tid är tillgängliga från Azure Portal. Om du vill hämta programmets installations datum och annan information aktiverar du diagnostik på gästnivå och dirigerar Windows-händelseloggen till Log Analytics-arbetsyta.

- [Aktivera Azure Automation inventerings samling för en virtuell dator](../automation/automation-tutorial-installed-software.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Delad

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: Azure Machine Learning har varierande stöd för olika beräknings resurser och till och med dina egna beräknings resurser. För beräknings resurser som ägs av din organisation använder du Azure Security Centerens fil integritets övervakning (FIM) för att identifiera all program vara som är installerad på virtuella datorer. Ett annat alternativ som kan användas i stället för eller tillsammans med FIM är Azure Automation Ändringsspårning och inventering för att samla in inventering från dina virtuella Linux-och Windows-datorer. 

Du kan implementera en egen process för att ta bort otillåten program vara. Du kan också använda en lösning från tredje part för att identifiera program som inte godkänts.

Ta bort Azure-resurser när de inte längre behövs.

- [Så här använder du övervakning av fil integritet](../security-center/security-center-file-integrity-monitoring.md)

- [Förstå Azure Automation Ändringsspårning och inventering](../automation/change-tracking/overview.md)

- [Så här aktiverar du inventering av virtuella Azure-datorer](../automation/automation-tutorial-installed-software.md)

- [Azure resurs grupp och borttagning av resurs](../azure-resource-manager/management/delete-resource-group.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: Azure Machine Learning har varierande stöd för olika beräknings resurser och till och med dina egna beräknings resurser. För beräknings resurser som ägs av din organisation använder du Azure Security Center anpassningsbara program kontroller för att säkerställa att endast auktoriserade program körs och all obehörig program vara blockeras från att köras på Azure Virtual Machines.

- [Använda Azure Security Center adaptiva program kontroller](../security-center/security-center-adaptive-application.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

* Otillåtna resurstyper
* Tillåtna resurstyper

Använd dessutom Azure Resource Graph för att fråga efter och identifiera resurser i prenumerationerna.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: underhåll en inventering av godkända program varu titlar

**Vägledning**: Azure Machine Learning har varierande stöd för olika beräknings resurser och till och med dina egna beräknings resurser. Använd Azure Security Center adaptiva program kontroller för att ange vilka filtyper en regel kan eller inte tillämpas på för beräknings resurser som ägs av din organisation.

Implementera en lösning från tredje part om adaptiva program kontroller inte uppfyller kravet.

- [Använda Azure Security Center adaptiva program kontroller](../security-center/security-center-adaptive-application.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd villkorlig åtkomst i Azure AD för att begränsa användarnas möjlighet att interagera med Azures resurs hanteraren genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".
 
- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azures resurs hanterare](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: Azure Machine Learning har varierande stöd för olika beräknings resurser och till och med dina egna beräknings resurser. För beräknings resurser som ägs av din organisation, beroende på typ av skript, kan du använda operativ system-/regionsspecifika konfigurationer eller resurser från tredje part för att begränsa användarnas möjlighet att köra skript i Azure Compute-resurser.  Du kan också använda Azure Security Center adaptiva program kontroller för att säkerställa att endast auktoriserade program körs och all obehörig program vara blockeras från att köras på Azure Virtual Machines.

- [Så här styr du körning av PowerShell-skript i Windows-miljöer](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

- [Använda Azure Security Center adaptiva program kontroller](../security-center/security-center-adaptive-application.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security benchmark: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: definiera och implementera standardkonfigurationer för Azure Machine Learning tjänsten med Azure policy. Använd Azure Policy alias i namn området "Microsoft. MachineLearning" om du vill skapa anpassade principer för granskning eller tillämpa konfigurationen för dina Azure Machine Learning-tjänster.

Azure Resource Manager kan exportera mallen i JavaScript Object Notation (JSON), som bör granskas för att säkerställa att konfigurationerna uppfyller säkerhets kraven för din organisation.

Du kan också använda rekommendationerna från Azure Security Center som en säker konfigurations bas linje för dina Azure-resurser.

Azure Machine Learning har fullt stöd för git-lagringsplatser för att spåra arbete. Du kan klona databaser direkt till din delade arbetsytefil, använda git på din lokala arbets Station och se till att säkra konfigurationer gäller för kod resurser som en del av din Machine Learning-miljö.

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Självstudie: skapa och hantera principer för att genomdriva efterlevnad](../governance/policy/tutorials/create-and-manage.md)

- [Exportera en och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Säkerhetsrekommendationer – en referensguide](../security-center/recommendations-reference.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

**Vägledning**: om beräknings resursen ägs av Microsoft, ansvarar Microsoft för operativ systemets säkra konfigurationer av Azure Machine Learning-tjänsten.

Azure Machine Learning har varierande stöd för olika beräknings resurser och till och med dina egna beräknings resurser. Använd Azure Security Center rekommendationer för att underhålla säkerhetskonfigurationer för alla beräknings resurser för beräknings resurser som ägs av din organisation.  Dessutom kan du använda anpassade operativ system avbildningar eller Azure Automation tillstånds konfiguration för att upprätta säkerhets konfigurationen för det operativ system som krävs av din organisation.

- [Så här övervakar du Azure Security Center rekommendationer](../security-center/security-center-recommendations.md)

- [Säkerhetsrekommendationer – en referensguide](../security-center/recommendations-reference.md)

- [Översikt över Azure Automation tillstånds konfiguration](../automation/automation-dsc-overview.md)

- [Ladda upp en virtuell hård disk och Använd den för att skapa nya virtuella Windows-datorer i Azure](../virtual-machines/windows/upload-generalized-managed.md)

- [Skapa en virtuell Linux-dator från en anpassad disk med Azure CLI](../virtual-machines/linux/upload-vhd.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Delad

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar i dina Azure-resurser. Dessutom kan du använda Azure Resource Manager mallar för att underhålla säkerhets konfigurationen för dina Azure-resurser som krävs av din organisation. 
 
 
 
- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)
 
- [Skapa och hantera principer för att använda kompatibilitet](../governance/policy/tutorials/create-and-manage.md)
 
- [ Översikt över Azure Resource Manager mallar](../azure-resource-manager/templates/overview.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: om beräknings resursen ägs av Microsoft, ansvarar Microsoft för operativ systemets säkra konfigurationer av Azure Machine Learning-tjänsten.

Azure Machine Learning har varierande stöd för olika beräknings resurser och till och med dina egna beräknings resurser. För beräknings resurser som ägs av din organisation följer du rekommendationer från Azure Security Center om att utföra sårbarhets bedömningar på dina Azure Compute-resurser.  Dessutom kan du använda Azure Resource Manager mallar, anpassade operativ Systems avbildningar eller Azure Automation tillstånds konfiguration för att underhålla säkerhets konfigurationen för det operativ system som krävs av din organisation.   Mallarna för Microsoft Virtual Machines tillsammans med Azure Automation tillstånds konfiguration kan hjälpa till att möta och upprätthålla säkerhets kraven. 

Observera att avbildningar av virtuella Azure Marketplace-datorer som publiceras av Microsoft hanteras och underhålls av Microsoft. 

- [Så här implementerar du rekommendationer för Azure Security Center sårbarhets bedömning](../security-center/deploy-vulnerability-assessment-vm.md)

- [Så här skapar du en virtuell Azure-dator från en ARM-mall](../virtual-machines/windows/ps-template.md)

- [Översikt över Azure Automation tillstånds konfiguration](../automation/automation-dsc-overview.md)

- [Skapa en virtuell Windows-dator i Azure Portal ](../virtual-machines/windows/quick-create-portal.md)

- [Information om hur du hämtar mallen för virtuella datorer](/previous-versions/azure/virtual-machines/windows/download-template)

- [Exempelskript för att överföra en virtuell hårddisk till Azure och skapa en ny virtuell dator](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Delad

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: om du använder anpassade Azure policys definitioner för dina Machine Learning eller relaterade resurser använder du Azure-databaser för att lagra och hantera din kod på ett säkert sätt.

Azure Machine Learning har fullt stöd för git-lagringsplatser för att spåra arbete. Du kan klona databaser direkt till din delade arbetsytefil, använda git på din lokala arbets Station och se till att säkra konfigurationer gäller för kod resurser som en del av din Machine Learning-miljö.

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Dokumentation om Azure databaser](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: Azure Machine Learning har varierande stöd för olika beräknings resurser och till och med dina egna beräknings resurser. För beräknings resurser som ägs av din organisation använder du rollbaserad åtkomst kontroll i Azure (Azure RBAC) så att endast behöriga användare kan komma åt dina anpassade avbildningar. Använd ett galleri för delade Azure-avbildningar du kan dela dina avbildningar till olika användare, tjänst huvud namn eller Azure AD-grupper i din organisation. Lagra behållar avbildningar i Azure Container Registry och Använd Azure RBAC för att säkerställa att endast auktoriserade användare har åtkomst.

- [Förstå Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Lär dig mer om Azure RBAC för Container Registry](../container-registry/container-registry-roles.md)

- [Så här konfigurerar du Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Översikt över delade avbildnings Galleri](../virtual-machines/shared-image-galleries.md)

- [Använd Azure RBAC för Kubernetes-auktorisering](../aks/manage-azure-rbac.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. MachineLearning" om du vill skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Dessutom kan du utveckla en process och pipeline för att hantera princip undantag.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Använda alias](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Distribuera konfigurations hanterings verktyg för operativ system

**Vägledning**: om beräknings resursen ägs av Microsoft ansvarar Microsoft för säker konfigurations distribution av Azure Machine Learning-tjänsten.

Azure Machine Learning har varierande stöd för olika beräknings resurser och till och med dina egna beräknings resurser. För beräknings resurser som ägs av din organisation använder du Azure Automation tillstånds konfiguration för DSC-noder (Desired State Configuration) i molnet eller det lokala data centret. Du kan enkelt publicera datorer, tilldela dem deklarativ konfigurationer och Visa rapporter som visar varje dators kompatibilitet med önskat tillstånd som du har angett. 

- [Så här aktiverar du Azure Automation tillstånds konfiguration](../automation/automation-dsc-onboarding.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Delad

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: Använd Azure Security Center för att utföra bas linjes ökningar för dina Azure-resurser. Du kan också använda Azure Policy för att varna och granska Azure-resursfiler.
 
 
 
- [ Så här åtgärdar du rekommendationer i Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: om beräknings resursen ägs av Microsoft är Microsoft ansvarig för automatisk säker konfigurations övervakning av Azure Machine Learning tjänst.

Azure Machine Learning har varierande stöd för olika beräknings resurser och till och med dina egna beräknings resurser. Använd Azure Security Center Compute &amp; Apps och följ rekommendationerna för virtuella datorer och servrar och behållare för beräknings resurser som ägs av din organisation.

- [Förstå rekommendationer för Azure Security Center-container](../security-center/container-security.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Delad

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Använd hanterade identiteter tillsammans med Azure Key Vault för att förenkla hemlig hantering för dina moln program.

Azure Machine Learning stöder kryptering av data lager med Kundhanterade nycklar måste du hantera nyckel rotation och återkalla säkerhet och krav för organisationen. 

Använd Azure Key Vault för att skicka hemligheter till fjärrkörning på ett säkert sätt i stället för klartext i dina utbildnings skript.

- [Azure Machine Learning Kundhanterade nycklar](concept-enterprise-security.md#azure-blob-storage)

- [Använd hemligheter för autentisering av autentiseringsuppgifter i Azure Machine Learning utbildnings körningar](how-to-use-secrets-in-runs.md)

- [Använda hanterade identiteter för Azure-resurser](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Så här skapar du en Key Vault](../key-vault/general/quick-create-portal.md)

- [Så här autentiserar du till Key Vault](../key-vault/general/authentication.md)

- [Så här tilldelar du en Key Vault åtkomst princip](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: Azure Machine Learning stöder både inbyggda roller och möjligheten att skapa anpassade roller. Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Med hanterade identiteter kan du autentisera till vilken tjänst som helst som stöder Azure AD-autentisering, inklusive Key Vault utan autentiseringsuppgifter i din kod.

 
- [Hantera åtkomst till en Azure Machine Learning-arbetsyta](how-to-assign-roles.md)

- [Konfigurera hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault. 

- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security benchmark: skydd mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: Använd centralt hanterad program vara för program mot skadlig kod

**Vägledning**: Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som stöder Azure-tjänster (till exempel Azure Machine Learning), men den körs inte på kund innehållet.

Azure Machine Learning har varierande stöd för olika beräknings resurser och till och med dina egna beräknings resurser. För beräknings resurser som ägs av din organisation använder du Microsoft Antimalware för Azure för att kontinuerligt övervaka och försvara dina resurser. För Linux använder du en lösning mot skadlig kod från tredje part. Använd också Azure Security Center s hot identifiering för data tjänster för att identifiera skadlig kod som laddats upp till lagrings konton.

- [Så här konfigurerar du Microsoft Antimalware för Azure](../security/fundamentals/antimalware.md)

- [Skydd mot hot i Azure Security Center](../security-center/azure-defender.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Delad

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som har stöd för Azure-tjänster (till exempel Azure Machine Learning), men det körs inte på kund innehåll.

Det är ditt ansvar att i förväg genomsöka allt innehåll som laddas upp till Azure-resurser som inte är Compute. Microsoft kan inte komma åt kunddata och kan därför inte utföra genomsökningar av kund innehåll för program mot skadlig kod.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för program mot skadlig kod uppdateras

**Vägledning**: Microsofts program mot skadlig kod har Aktiver ATS och underhålls för den underliggande värden som har stöd för Azure-tjänster (till exempel Azure Machine Learning), men det körs inte på kund innehåll.

Azure Machine Learning har varierande stöd för olika beräknings resurser och till och med dina egna beräknings resurser. För alla beräknings resurser som ägs av din organisation följer du rekommendationer i Azure Security Center, beräknar &amp; program för att se till att alla slut punkter är uppdaterade med de senaste signaturerna. För Linux använder du en lösning mot skadlig kod från tredje part.

- [Så här distribuerar du Microsoft Antimalware för Azure](../security/fundamentals/antimalware.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Delad

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Azure Security benchmark: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: se till att vanlig automatisk säkerhets kopiering UPS

**Vägledning**: data återställnings hantering i Machine learnings tjänsten är via data hantering på anslutna data lager. Kontrol lera att du följer rikt linjerna för att återställa data för anslutna butiker för att säkerhetskopiera data per kund organisations principer.

- [Återställa filer från säkerhets kopiering av virtuella Azure-datorer](../backup/backup-azure-restore-files-from-vm.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: data säkerhets kopiering i Machine learnings tjänsten är via data hantering på anslutna data lager. Aktivera Azure Backup för virtuella datorer och konfigurera önskade frekvenser och kvarhållningsperiod. Säkerhetskopiera Kundhanterade nycklar i Azure Key Vault.

- [Återställa filer från säkerhets kopiering av virtuella Azure-datorer](../backup/backup-azure-restore-files-from-vm.md)
- [Så här återställer du Key Vault nycklar i Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: verifiering av data säkerhets kopiering i Machine learnings tjänsten är via data hantering på anslutna data lager. Regelbundet utföra Data återställning av innehåll i Azure Backup. Se till att du kan återställa säkerhetskopierade nycklar som hanteras av kunden.

- [Återställa filer från en säkerhets kopia av en virtuell Azure-dator](../backup/backup-azure-restore-files-from-vm.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: för lokal säkerhets kopiering tillhandahålls kryptering i vila med den lösen fras som du anger när du säkerhetskopierar till Azure. Använd rollbaserad åtkomst kontroll i Azure för att skydda säkerhets kopior och Kundhanterade nycklar. 

Aktivera mjuk borttagning och tömning av skydd i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning. Om Azure Storage används för att lagra säkerhets kopior aktiverar du mjuk borttagning för att spara och återställa data när blobbar eller BLOB-ögonblicksbilder tas bort.
 
 
- [Förstå Azure RBAC](../role-based-access-control/overview.md)

- [Aktivera mjuk borttagning och rensningsskydd i Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Mjuk borttagning för Azure Blob Storage](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security benchmark: incident svar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: utveckla en incident svars guide för din organisation. Se till att det finns skriftliga svars planer för incidenter som definierar alla roller för personal samt de olika faserna av incident hantering och hantering från identifiering till granskning efter incidenten. 

- [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Använd NIST hanterings guide för dator säkerhet för att hjälpa till med att skapa din egen incident svars plan](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Azure Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller den analytiskt som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Dessutom kan du markera prenumerationer med taggar och skapa ett namngivnings system för att identifiera och kategorisera Azure-resurser, särskilt för bearbetning av känsliga data. Det är ditt ansvar att prioritera reparationen av aviseringar baserat på allvarlighets graden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems funktioner för incident svar på en vanlig takt för att hjälpa till att skydda dina Azure-resurser. Identifiera svaga punkter och luckor och ändra sedan svars planen efter behov.

- [NISTs publikation – guide för att testa, träna och träna program för IT-planer och-funktioner](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en olagligt eller obehörig part. Granska incidenter när du är säker på att problemen är lösta.

- [Konfigurera en säkerhetskontakt i Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export för att identifiera risker för Azure-resurser. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöde Azure Security Center för att automatiskt utlösa svar på säkerhets aviseringar och rekommendationer för att skydda dina Azure-resurser.

- [Konfigurera automatisering av arbets flöden i Security Center](../security-center/workflow-automation.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstester och Red Team-tester

*Mer information finns i [övningen för Azure Security benchmark: inträngande tester och röda team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: Följ Microsoft Cloud inträngande test för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft.

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Delad

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)