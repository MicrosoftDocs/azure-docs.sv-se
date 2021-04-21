---
title: Säkerhetsbaslinje för Automation i Azure
description: Säkerhetsbaslinjen för Automation innehåller procedurvägledning och resurser för implementering av säkerhetsrekommendationerna som anges i Azure Security Benchmark.
author: msmbaldwin
ms.service: automation
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 7f87fcac0c1b07e108082c4b4f48bde046dc63c7
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834506"
---
# <a name="azure-security-baseline-for-automation"></a>Säkerhetsbaslinje för Automation i Azure

Den här säkerhetsbaslinjen tillämpar vägledning [från Azure Security Benchmark version 1.0](../security/benchmarks/overview-v1.md) för Azure Automation. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure.
Innehållet grupperas efter de säkerhetskontroller **som definierats** av Azure Security Benchmark och tillhörande vägledning som gäller för Azure Automation. **Kontroller** som inte är Azure Automation har undantagits.

 
Information om hur Azure Automation mappning helt till Azure Security Benchmark finns i den fullständiga [mappningsfilen Azure Automation säkerhetsbaslinje.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Skydda Azure-resurser i virtuella nätverk

**Vägledning:** Azure Automation har ännu inte stöd för Azure Private Link för att begränsa åtkomsten till tjänsten via privata slutpunkter. Runbooks som autentiserar och körs mot resurser i Azure körs i en Sandbox-miljö i Azure och använder delade backend-resurser som Microsoft ansvarar för att isolera från varandra. deras nätverk är obegränsade och har åtkomst till offentliga resurser. Azure Automation har för närvarande inte integrering av virtuella nätverk för privata nätverk utöver stödet för Hybrid Runbook Workers. Den här kontrollen gäller inte om du använder den intjänade tjänsten utan Hybrid Runbook Workers.

Om du vill isolera dina runbooks ytterligare kan du använda Hybrid Runbook Workers som körs på virtuella Azure-datorer. När du skapar en virtuell Azure-dator måste du skapa ett virtuellt nätverk (VNet) eller använda ett befintligt virtuellt nätverk och konfigurera dina virtuella datorer med ett undernät. Se till att alla distribuerade undernät har en nätverkssäkerhetsgrupp tillämpad med nätverksåtkomstkontroller som är specifika för dina program betrodda portar och källor. För tjänstspecifika krav, se säkerhetsrekommendationen för den specifika tjänsten. Om du har ett specifikt krav kan Azure Firewall också användas för att uppfylla det.

- [Virtuella nätverk och virtuella datorer i Azure](../virtual-machines/network-overview.md)

- [Så här skapar du en Virtual Network](../virtual-network/quick-create-portal.md)

- [Så här skapar du en NSG med en säkerhetskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

- [Så här distribuerar och konfigurerar du Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Runbook-körningsmiljö](./automation-runbook-execution.md#runbook-execution-environment)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Övervaka och logga konfiguration och trafik för virtuella nätverk, undernät och nätverksgränssnitt

**Vägledning:** Azure Automation för närvarande inte har integrering av virtuella nätverk för privata nätverk utöver stödet för Hybrid Runbook Workers. Den här kontrollen gäller inte om du använder den in-of-box-tjänsten utan Hybrid Runbook Workers.

Om du använder Hybrid Runbook Workers som backas upp av virtuella Azure-datorer ser du till att undernätet som innehåller dessa arbetare är aktiverat med en nätverkssäkerhetsgrupp (NSG) och konfigurerar flödesloggar för att vidarebefordra loggar till ett lagringskonto för trafikgranskning. Du kan också vidarebefordra NSG-flödesloggar till en Log Analytics-arbetsyta och använda Trafikanalys för att ge insikter om trafikflödet i ditt Azure-moln. Vissa fördelar med Trafikanalys är möjligheten att visualisera nätverksaktivitet och identifiera hotpunkter, identifiera säkerhetshot, förstå trafikflödesmönster och hitta felkonfigurationer i nätverk.

NSG-regler och användardefinierade vägar gäller inte för privata slutpunkter, men NSG-flödesloggar och övervakningsinformation för utgående anslutningar stöds fortfarande och kan användas.

- [Så här aktiverar du NSG-flödesloggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Aktivera och använda Trafikanalys](../network-watcher/traffic-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

**Vägledning:** Azure Automation för närvarande inte har integrering av virtuella nätverk för privata nätverk utöver stödet för Hybrid Runbook Workers. Den här kontrollen gäller inte om du använder den in-of-box-tjänsten utan Hybrid Runbook Workers.

Om du använder Hybrid Runbook Workers som backas upp av virtuella Azure-datorer aktiverar du DDoS-standardskydd (Distributed Denial of Service) på dina virtuella nätverk som är värdar för dina Hybrid Runbook Workers för att skydda dig mot DDoS-attacker. Genom att Azure Security Center Integrated Threat Intelligence kan du neka kommunikation med kända skadliga IP-adresser.  Konfigurera Azure Firewall på varje Virtual Network segment med Hotinformation aktiverat och konfigurera för att varna och neka **skadlig** nätverkstrafik.

Du kan använda Azure Security Center just-in-time-nätverk för att begränsa exponeringen av virtuella Windows-datorer till godkända IP-adresser under en begränsad tidsperiod.  Använd även Azure Security Center rekommendationer för nätverkshärdning för NSG-konfigurationer för att begränsa portar och käll-IP-adresser baserat på faktisk trafik och hotinformation.

- [Så här konfigurerar du DDoS-skydd](../ddos-protection/manage-ddos-protection.md)

- [Så här distribuerar du Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Förstå Azure Security Center Integrated Threat Intelligence](../security-center/azure-defender.md)

- [Förstå Azure Security Center anpassningsbar nätverkshärdning](../security-center/security-center-adaptive-network-hardening.md)

- [Förstå Azure Security Center just-in-time-Access Control](../security-center/security-center-just-in-time.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="15-record-network-packets"></a>1.5: Registrera nätverkspaket

**Vägledning:** Azure Automation för närvarande inte har integrering av virtuella nätverk för privata nätverk utöver stödet för Hybrid Runbook Workers, gäller inte den här kontrollen om du använder den lokala tjänsten utan Hybrid Workers.

Om du använder Hybrid Runbook Workers som backas upp av virtuella Azure-datorer kan du registrera NSG-flödesloggar till ett lagringskonto för att generera flödesposter för dina Azure Virtual Machines som fungerar som Runbook Workers. När du undersöker avvikande aktivitet kan du aktivera Network Watcher paketinfångning så att nätverkstrafiken kan granskas för ovanlig och oväntad aktivitet.

- [Så här aktiverar du NSG-flödesloggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar du Network Watcher](../network-watcher/network-watcher-create.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuera nätverksbaserade system för intrångsidentifiering/intrångsskydd (IDS/IPS)

**Vägledning:** Azure Automation för närvarande inte har integrering av virtuella nätverk för privata nätverk utöver stödet för Hybrid Runbook Workers. Den här kontrollen gäller inte om du använder den in-of-box-tjänsten utan Hybrid Runbook Workers.

Om du använder Hybrid Runbook Worker på virtuella Azure-datorer kan du kombinera paketinfångst som tillhandahålls av Network Watcher och IDS-verktyg med öppen källkod för att utföra identifiering av nätverksintrång för en mängd olika hot mot arbetsdatorerna. Du kan också distribuera Azure Firewall till Virtual Network segment efter behov med Hotinformation aktiverat och konfigurerat till "Avisering och neka" för skadlig nätverkstrafik.

- [Utföra identifiering av nätverksintrång med Network Watcher och verktyg med öppen källkod](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

- [Så här distribuerar du Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Så här konfigurerar du aviseringar med Azure Firewall](../firewall/threat-intel.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexitet och administrativa kostnader för nätverkssäkerhetsregler

**Vägledning:** Använd Virtual Network tjänsttaggar för att definiera nätverksåtkomstkontroller för nätverkssäkerhetsgrupper eller nätverkssäkerhetsgrupper Azure Firewall konfigurerade i Azure som kräver åtkomst till dina Automation-resurser. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttaggnamnet (till exempel GuestAndHybridManagement) i lämpligt käll- eller målfält för en regel kan du tillåta eller neka trafik för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras.

- [Förstå och använda tjänsttaggar](../virtual-network/service-tags-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Underhålla standardsäkerhetskonfigurationer för nätverksenheter

**Vägledning:** Definiera och implementera standardsäkerhetskonfigurationer för nätverksresurser som används av Azure Automation med Azure Policy.

Du kan också använda Azure Blueprints för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljöartefakter, till exempel Azure Resources Manager-mallar, Azure RBAC-kontroller och principer, i en enda skissdefinition. Du kan tillämpa skissen på nya prenumerationer och finjustera kontroll och hantering via versionshantering.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy exempel för nätverk](../governance/policy/samples/built-in-policies.md#network)

- [Så här skapar du en Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentera trafikkonfigurationsregler

**Vägledning:** Använda taggar för NSG:er och andra resurser som rör nätverkssäkerhet och trafikflöde. För enskilda NSG-regler använder du fältet "Beskrivning" för att ange affärs behov och/eller varaktighet (osv.) för regler som tillåter trafik till/från ett nätverk.

Använd någon av de inbyggda Azure Policy-definitioner som rör taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och för att meddela dig om befintliga otaggade resurser.

Du kan använda Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

- [Så här skapar du en Virtual Network](../virtual-network/quick-create-portal.md)

- [Så här skapar du en NSG med en säkerhetskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

**Vägledning:** Använda Azure-aktivitetsloggen för att övervaka resurskonfigurationer och identifiera ändringar i dina nätverksresurser. Skapa aviseringar inom Azure Monitor som utlöses när ändringar av kritiska resurser sker.

- [Så här visar och hämtar du Händelser i Azure-aktivitetsloggen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Skapa aviseringar i Azure Monitor](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i Azure Security Benchmark: Logging and Monitoring ( [Prestandatest för Azure-säkerhet: Loggning och övervakning).](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera central hantering av säkerhetsloggar

**Vägledning:** Vidarebefordra loggdata till Azure Monitor loggar för att aggregera säkerhetsdata som genereras av Azure Automation resurser. Inom Azure Monitor kan du använda loggfrågor för att söka efter och utföra analyser och använda Azure Storage-konton för långsiktig lagring/arkiveringslagring. Azure Automation kan skicka status för runbook-jobb, jobbströmmar, Automation-tillståndskonfigurationsdata, uppdateringshantering och spårning av ändringar eller inventeringsloggar till Log Analytics-arbetsytan. Den här informationen visas från API:Azure Portal, Azure PowerShell och Azure Monitor Logs, vilket gör att du kan utföra enkla undersökningar.

Du kan också aktivera och ta med data för att Azure Sentinel eller en SIEM från tredje part. 

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Samla in plattformsloggar och mått med Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Så här kommer du igång Azure Monitor siem-integrering från tredje part](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Vidarebefordra jobbdata från Azure Automation till Azure Monitor-loggar](automation-manage-send-joblogs-log-analytics.md)

- [Integrera DSC med Azure Monitor loggar](automation-dsc-diagnostics.md)

- [Regioner som stöder länkade Log Analytics-arbetsytor](how-to/region-mappings.md)

- [Fråga Uppdateringshantering loggar](update-management/query-logs.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägledning:** Aktivera Azure Monitor åtkomst till gransknings- och aktivitetsloggar som innehåller händelsekälla, datum, användare, tidsstämpel, källadresser, måladresser och andra användbara element. 

- [Samla in plattformsloggar och mått med Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Visa och hämta Händelser i Azure-aktivitetsloggen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera kvarhållning av säkerhetslogglagring

**Vägledning:** Inom Azure Monitor anger du kvarhållningsperioden för Log Analytics-arbetsytan enligt organisationens efterlevnadsregler. Använd Azure Storage-konton för långsiktig lagring/arkiveringslagring.

- [Ändra kvarhållningsperioden för data i Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Information om databevarande för Automation-konton](./automation-managing-data.md#data-retention)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning:** Analysera och övervaka loggar för avvikande beteende och granska regelbundet resultat. Använd Azure Monitor för att granska loggar och köra frågor på loggdata.

Du kan också aktivera och ta med data till Azure Sentinel siem eller siem från tredje part. 

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Förstå loggfrågor i Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md)

- [Så här utför du anpassade frågor i Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivera aviseringar för avvikande aktiviteter

**Vägledning:** Använd Azure Security Center med Azure Monitor för övervakning och aviseringar om avvikande aktivitet som finns i säkerhetsloggar och händelser.

Du kan också aktivera och ta med data för att Azure Sentinel.

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Hantera aviseringar i Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Avisering om Azure Monitor loggdata](../azure-monitor/alerts/tutorial-response.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivera DNS-frågeloggning

**Vägledning:** Implementera en tredjepartslösning från en Azure Marketplace DNS-loggningslösning enligt organisationens behov.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Upprätthålla en förteckning över administrativa konton

**Vägledning:** Använd Azure Active Directory inbyggda administratörsroller (Azure AD) som kan tilldelas explicit och kan efterfrågas. Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper. När du använder Kör som-konton för Automation-konton för dina runbooks bör du se till att dessa tjänsthuvudnamn också spåras i ditt lager eftersom de ofta har förhöjd behörighet. Ta bort eventuella oanvända Kör som-konton för att minimera den exponerade attackytan.

- [Så här får du en katalogroll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Så här hämtar du medlemmar i en katalogroll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Ta bort ett Kör som-konto eller ett klassiskt Kör som-konto](delete-run-as-account.md)

- [Hantera ett kör Azure Automation kör som-konto](manage-runas-account.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändra standardlösenord i förekommande fall

**Vägledning:** Azure Automation-konto har inte begreppet standardlösenord.  Kunder ansvarar för program från tredje part och Marketplace-tjänster som kan använda standardlösenord som körs ovanpå tjänsten eller dess Hybrid Runbook Workers.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Använd dedikerade administrativa konton

**Vägledning:** Skapa standardprocedurer för användning av dedikerade administrativa konton. Använd Azure Security Center identitets- och åtkomsthantering för att övervaka antalet administrativa konton. När du använder Kör som-konton för Automation-konton för dina runbooks bör du se till att dessa tjänsthuvudnamn också spåras i din inventering eftersom de ofta har förhöjd behörighet. Omfångsomfång för dessa identiteter med de lägsta privilegierade behörigheter som de behöver för att dina runbooks ska kunna utföra sin automatiserade process. Ta bort eventuella oanvända Kör som-konton för att minimera den exponerade attackytan.

Du kan också aktivera just-in-time/just-enough-åtkomst med hjälp av Azure Active Directory (Azure AD) Privileged Identity Management privilegierade roller för Microsoft-tjänster och Azure Resource Manager.

- [Läs mer om Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

- [Ta bort ett Kör som-konto eller ett klassiskt Kör som-konto](delete-run-as-account.md)

- [Hantera ett kör Azure Automation kör som-konto](manage-runas-account.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory för enkel inloggning (SSO)

**Vägledning:** Om möjligt ska du använda enkel inloggning med Azure Active Directory (Azure AD) i stället för att konfigurera enskilda fristående autentiseringsuppgifter per tjänst. Använd Azure Security Center rekommendationer för identitets- och åtkomsthantering.

- [Enkel inloggning till program i Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Övervaka identitet och åtkomst inom Azure Security Center](../security-center/security-center-identity-access.md)

- [Använda Azure AD för att autentisera mot Azure](automation-use-azure-ad.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använd multifaktorautentisering för Azure Active Directory åtkomstbaserad åtkomst

**Vägledning:** Aktivera Azure Active Directory (Azure AD) multifaktorautentisering och följ Azure Security Center rekommendationer för identitets- och åtkomsthantering.

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst inom Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Använd dedikerade datorer (arbetsstationer för privilegierad åtkomst) för alla administrativa uppgifter

**Vägledning:** Använd PAWs med multifaktorautentisering som konfigurerats för att logga Azure Automation konfigurera kontoresurser i produktionsmiljöer. 

- [Läs mer om arbetsstationer med privilegierad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logga och avisering om misstänkta aktiviteter från administrativa konton 

**Vägledning:** Använda Azure Active Directory (Azure AD) riskidentifiering för att visa aviseringar och rapporter om riskfyllda användarbeteenden. Alternativt kan kunden vidarebefordra aviseringar Azure Security Center riskidentifiering för att Azure Monitor konfigurera anpassade aviseringar/meddelanden med hjälp av åtgärdsgrupper.

- [Förstå Azure Security Center riskidentifiering (misstänkt aktivitet)](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Så här konfigurerar du åtgärdsgrupper för anpassade aviseringar och meddelanden](../azure-monitor/alerts/action-groups.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Hantera Azure-resurser från endast godkända platser 

**Vägledning:** Vi rekommenderar att du använder namngivna platser för villkorsstyrd åtkomst för att endast tillåta åtkomst från specifika logiska gruppningar av IP-adressintervall eller länder/regioner. 

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning:** Använd Azure Active Directory (Azure AD) som centralt system för autentisering och auktorisering. Azure AD skyddar data med hjälp av stark kryptering för vilodata och under överföring. Azure AD använder också salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt. Om du använder Hybrid Runbook Workers kan du använda hanterade identiteter i stället för Kör som-konton för att möjliggöra smidigare säkra behörigheter.

- [Skapa och konfigurera en Azure AD-instans](../active-directory-domain-services/tutorial-create-instance.md)

- [Använda runbook-autentisering med hanterade identiteter](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Granska och stämma av användaråtkomst regelbundet

**Vägledning:** Azure Active Directory (Azure AD) innehåller loggar som hjälper dig att identifiera inaktuella konton. Använd dessutom Azure-identitetsåtkomstgranskningar för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Användaråtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst. När du använder Kör som-konton för Automation-konton för dina runbooks ser du till att dessa tjänsthuvudnamn också spåras i ditt lager eftersom de ofta har förhöjd behörighet. Ta bort eventuella oanvända Kör som-konton för att minimera den exponerade attackytan.

- [Förstå Azure AD-rapportering](../active-directory/reports-monitoring/index.yml)

- [Så här använder du azure-identitetsåtkomstgranskningar](../active-directory/governance/access-reviews-overview.md)

- [Ta bort ett Kör som-konto eller ett klassiskt Kör som-konto](delete-run-as-account.md)

- [Hantera ett kör Azure Automation kör som-konto](manage-runas-account.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning:** Du har åtkomst till Azure Active Directory(Azure AD) inloggningsaktivitet, gransknings- och riskhändelseloggkällor, vilket gör att du kan integrera med valbara SIEM-/övervakningsverktyg.

Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka granskningsloggar och inloggningsloggar till en Log Analytics-arbetsyta. Du kan konfigurera önskade aviseringar i Log Analytics-arbetsytan.

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Avisering om kontots beteendeavvikelse vid inloggning

**Vägledning:** Använd Azure Active Directory (Azure AD) Risk- och Identity Protection-funktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder relaterade till användaridentiteter för din nätverksresurs. Du kan också mata in data i Azure Sentinel för ytterligare undersökning.

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du riskprinciper för Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ge Microsoft åtkomst till relevanta kunddata under supportscenarier

**Vägledning:** För Azure Automation-konton kan Microsoft Support komma åt plattformens resursmetadata under ett öppet supportfall utan användning av något annat verktyg.

Men när du använder Hybrid Runbook Workers som backas upp av virtuella Azure-datorer och en tredje part behöver åtkomst till kunddata (till exempel under en supportbegäran) använder du Customer Lockbox (förhandsversion) för virtuella Azure-datorer för att granska och godkänna eller avvisa begäranden om kunddataåtkomst.

- [Förstå Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Upprätthålla en förteckning över känslig information

**Vägledning:** Använd taggar för att spåra Azure Automation som lagrar eller bearbetar känslig information. 

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolera system som lagrar eller bearbetar känslig information

**Vägledning:** Implementera separata prenumerationer och/eller hanteringsgrupper för utveckling, testning och produktion. Isolera miljöer med hjälp av separata Automation-kontoresurser. Resurser som Hybrid Runbook Workers ska avgränsas med virtuellt nätverk/undernät, taggas på rätt sätt och skyddas i en nätverkssäkerhetsgrupp (NSG) eller Azure Firewall. För virtuella datorer som lagrar eller bearbetar känsliga data implementerar du policyer och procedurer för att inaktivera dem när de inte används.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

- [Så här skapar du en Virtual Network](../virtual-network/quick-create-portal.md)

- [Så här skapar du en NSG med en säkerhetskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

- [Så här distribuerar du Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Så här konfigurerar du avisering eller avisering och nekar med Azure Firewall](../firewall/threat-intel.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Övervaka och blockera obehörig överföring av känslig information

**Vägledning:** När du använder Hybrid Runbook Worker-funktionen kan du använda en tredjepartslösning från Azure Marketplace i nätverks perimeternätverk som övervakar obehörig överföring av känslig information och blockerar sådana överföringar samtidigt som informationssäkerhetspersonal varnas.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt och skyddar mot förlust och exponering av kunddata. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och upprätthåller en uppsättning robusta dataskyddskontroller och -funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Kryptera all känslig information under överföring

**Vägledning:** Kryptera all känslig information under överföring. Se till att alla klienter som ansluter till dina Azure-resurser i virtuella Azure-nätverk kan förhandla TLS 1.2 eller senare. Azure Automation fullständigt stöd för och framtvingar transportlager (TLS) 1.2 och alla klientanrop eller senare versioner för alla externa HTPS-slutpunkter (via webhooks, DSC-noder, Hybrid Runbook Worker).

Följ Azure Security Center rekommendationer för kryptering i vila och kryptering under överföring, om tillämpligt.

- [Förstå kryptering under överföring med Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Azure Automation tvingande TLS 1.2](../active-directory/hybrid/reference-connect-tls-enforcement.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Använd ett aktivt identifieringsverktyg för att identifiera känsliga data

**Vägledning:** Använd ett aktivt identifieringsverktyg från tredje part för att identifiera all känslig information som lagras, bearbetas eller överförs av organisationens tekniksystem, inklusive sådana som finns på plats eller hos en fjärransluten tjänstleverantör och uppdatera organisationens känsliga informationslager.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Använd rollbaserad åtkomstkontroll för att styra åtkomsten till resurser

**Vägledning:** Använd rollbaserad åtkomstkontroll i Azure (Azure RBAC) för att styra åtkomsten till Azure Automation-resurser med hjälp av de inbyggda rolldefinitionerna, tilldela åtkomst för användare som har åtkomst till dina automationsresurser enligt en minst privilegierad eller "just-enough"-åtkomstmodell. När du använder Hybrid Runbook Workers ska du använda hanterade identiteter för de virtuella datorerna för att undvika att använda tjänstens huvudnamn. När du använder både Runbook Workers för flera klienter eller Hybrid Runbook Workers måste du tillämpa korrekt begränsade Azure RBAC-behörigheter för Runbook Workers-identiteten.

- [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Runbook-behörigheter för en Hybrid Runbook Worker](./automation-hybrid-runbook-worker.md#runbook-permissions-for-a-hybrid-runbook-worker)

- [Hantera rollbehörigheter och säkerhet](automation-role-based-access-control.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Använd värdbaserad dataförlustskydd för att framtvinga åtkomstkontroll

**Vägledning:** Azure Automation för närvarande inte exponerar den underliggande runbook worker-datorn för flera innehavare och den hanteras av plattformen. Den här kontrollen gäller inte om du använder den in-of-box-tjänsten utan Hybrid Runbook Workers.

Om du använder Hybrid Runbook Workers som backas upp av virtuella Azure-datorer måste du använda en värdbaserad lösning för dataförlustskydd från tredje part för att framtvinga åtkomstkontroller till dina värdbaserade Hybrid Runbook Worker virtuella datorer.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Kryptera känslig information i vila

**Vägledning:** Använda kund hanterade nycklar med Azure Automation. Azure Automation har stöd för användning av kund hanterade nycklar för att kryptera alla "säkra tillgångar" som används, till exempel autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Använd krypterade variabler med dina runbooks för att all din beständiga variabeluppslag behöver förhindra oavsiktlig exponering.

När du använder Hybrid Runbook Workers krypteras de virtuella diskarna på de virtuella datorerna i vila med antingen kryptering på serversidan eller Azure-diskkryptering (ADE). Azure-diskkryptering utnyttjar BitLocker-funktionen i Windows för att kryptera hanterade diskar med kund hanterade nycklar på den virtuella gästdatorn. Kryptering på serversidan med kund hanterade nycklar förbättrar ADE genom att göra det möjligt för dig att använda alla typer av operativsystem och avbildningar för dina virtuella datorer genom att kryptera data i lagringstjänsten.

- [Kryptering på serversidan av Azure-hanterade diskar](../virtual-machines/disk-encryption.md)

- [Azure Disk Encryption för virtuella Windows-datorer](../virtual-machines/windows/disk-encryption-overview.md)

- [Användning av kund hanterade nycklar för ett Automation-konto](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Hanterade variabler i Azure Automation](shared-resources/variables.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Automation:**

[!INCLUDE [Resource Policy for Microsoft.Automation 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.automation-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisering om ändringar av viktiga Azure-resurser

**Vägledning:** Använd Azure Monitor med Azure-aktivitetsloggen för att skapa aviseringar för när ändringar sker i viktiga Azure-resurser som nätverkskomponenter, Azure Automation-konton och runbooks. 

- [Diagnostisk loggning för en nätverkssäkerhetsgrupp](../private-link/private-link-overview.md#logging-and-monitoring)

- [Så här skapar du aviseringar för Azure-aktivitetslogghändelser](../azure-monitor/alerts/alerts-activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Köra automatiserade sårbarhetsgenomsökningsverktyg

**Vägledning:** Följ rekommendationerna från Azure Security Center att utföra sårbarhetsbedömningar på dina Azure-resurser

- [Säkerhetsrekommendationer i Azure Security Center](../security-center/security-center-recommendations.md)

- [Security Center rekommendationsreferens](../security-center/recommendations-reference.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Jämför tillbaka-till-tillbaka-sårbarhetsgenomsökningar

**Vägledning:** Exportera genomsökningsresultat med konsekventa intervall och jämför resultaten för att verifiera att sårbarheter har åtgärdats. När du använder rekommendationen för sårbarhetshantering Azure Security Center kan kunden pivotera till den valda lösningens portal för att visa historiska genomsökningsdata.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Använd en riskklassificeringsprocess för att prioritera åtgärder för identifierade säkerhetsrisker

**Vägledning:** Använd standardriskklassificeringar (säkerhetspoäng) som tillhandahålls av Azure Security Center att hjälpa till att prioritera åtgärder för identifierade säkerhetsrisker.

- [Förstå Azure Security Center säkerhetspoäng](../security-center/secure-score-security-controls.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security Benchmark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Använd automatisk tillgångsidentifiering

**Vägledning:** Använd Azure Resource Graph för att fråga efter och identifiera Azure Automation resurser i dina prenumerationer. Se till att du har rätt (läsbehörighet) i din klientorganisation och kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="62-maintain-asset-metadata"></a>6.2: Underhålla tillgångsmetadata

**Vägledning:** Tillämpa taggar på Azure-resurser som ger metadata för att organisera dem logiskt i en taxonomi. 

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ta bort obehöriga Azure-resurser

**Vägledning:** Använd taggar, hanteringsgrupper och separata prenumerationer när det är lämpligt för att organisera och spåra Azure Automation resurser. Stämma av inventeringen regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid. Ta bort eventuella oanvända Kör som-konton för att minimera den exponerade attackytan.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

- [Ta bort ett Kör som-konto eller ett klassiskt Kör som-konto](delete-run-as-account.md)

- [Hantera ett kör Azure Automation kör som-konto](manage-runas-account.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definiera och underhålla inventering av godkända Azure-resurser

**Vägledning:** Du måste skapa en förteckning över godkända Azure-resurser och godkänd programvara för beräkningsresurser enligt organisationens behov.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Övervaka för ej godkända Azure-resurser

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner: 

- Otillåtna resurstyper 
- Tillåtna resurstyper 

Dessutom kan du använda Azure Resource Graph för att fråga/identifiera resurser i prenumerationer. Detta kan vara till hjälp i miljöer med hög säkerhet, till exempel de med Lagringskonton. 

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Azure Policy inbyggda exempel för Azure Automation](policy-reference.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Ta bort ej godkända Azure-resurser och program

**Vägledning:** Kunden kan förhindra att resurser skapas eller används med Azure Policy enligt kundens företagsriktlinjer. Du kan implementera en egen process för att ta bort obehöriga resurser. I Azure Automation-erbjudandet är det möjligt att installera, ta bort och hantera De PowerShell- eller Python-moduler som runbooks kan komma åt via portalen eller cmdlets. Ej godkänd eller gammal modul bör tas bort eller uppdateras för runbooks.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Hantera modul i Azure Automation](shared-resources/modules.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="69-use-only-approved-azure-services"></a>6.9: Använd endast godkända Azure-tjänster

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en viss resurstyp med Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning:** Använd Principer för villkorsstyrd åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "Blockera åtkomst" för Microsoft Azure Management-appen från oskyddade eller ej godkända platser eller enheter. 

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security Benchmark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning:** Använd Azure Policy alias för att skapa anpassade principer för att granska eller framtvinga konfigurationen av Azure Automation och relaterade resurser. Du kan också använda inbyggda Azure Policy definitioner.

Dessutom Azure Resource Manager kan exportera mallen i JavaScript Object Notation (JSON), som bör granskas för att säkerställa att konfigurationerna uppfyller/överskrider säkerhetskraven för din organisation.

Du kan också använda rekommendationer från Azure Security Center som en säker konfigurationsbaslinje för dina Azure-resurser.

- [Så här visar du tillgängliga Azure Policy-alias](/powershell/module/az.resources/get-azpolicyalias)

- [Självstudie: Skapa och hantera principer för att framtvinga efterlevnad](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy exempel på inbyggda Azure Automation](policy-reference.md)

- [Export av en enskild resurs och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Säkerhetsrekommendationer – en referensguide](../security-center/recommendations-reference.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Upprätthålla säkra Azure-resurskonfigurationer

**Vägledning:** Använd Azure Resource Manager mallar och Azure Policy för att konfigurera Azure-resurser som är associerade med Azure Automation. Azure Resource Manager är JSON-baserade filer som används för att distribuera Azure-resurser och alla anpassade mallar måste lagras och underhållas på ett säkert sätt på en kodlagringsplats. Använd funktionen för källkontrollsintegrering för att hålla dina runbooks i ditt Automation-konto uppdaterade med skript på lagringsplatsen för källkontroll. Använd Azure Policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar för alla dina Azure-resurser.

- [Använda källkontrollsintegrering](source-control-integration.md)

- [Information om hur du Azure Resource Manager mallar](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) 

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Förstå Azure Policy effekter](../governance/policy/concepts/effects.md)

- [Distribuera ett Automation-konto med en Azure Resource Manager mall](/azure/automation/quickstart-create-automation-account-template)

- [Azure Policy exempel på inbyggda Azure Automation](policy-reference.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Lagra konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning:** Använd Azure DevOps för att lagra och hantera din kod på ett säkert sätt, till exempel anpassade Azure-principer, Azure Resource Manager-mallar och Desired State Configuration-skript. För att få åtkomst till de resurser som du hanterar i Azure DevOps kan du bevilja eller neka behörigheter till specifika användare, inbyggda säkerhetsgrupper eller grupper som definierats i Azure Active Directory (Azure AD) om de är integrerade med Azure DevOps eller Active Directory om de är integrerade med TFS. Använd funktionen för källkontrollsintegrering för att hålla dina runbooks i ditt Automation-konto uppdaterade med skript på lagringsplatsen för källkontroll.

- [Lagra kod i Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Om behörigheter och grupper i Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Använda källkontrollsintegrering](source-control-integration.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuera konfigurationshanteringsverktyg för Azure-resurser

**Vägledning:** Definiera och implementera standardsäkerhetskonfigurationer för Azure-resurser med hjälp av Azure Policy. Använd Azure Policy alias för att skapa anpassade principer för att granska eller framtvinga nätverkskonfigurationen för dina Azure-resurser. Du kan också använda inbyggda principdefinitioner som är relaterade till dina specifika resurser. 

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här använder du alias](../governance/policy/concepts/definition-structure.md#aliases)

- [Azure Policy inbyggda exempel för Azure Automation](policy-reference.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementera automatiserad konfigurationsövervakning för Azure-resurser

**Vägledning:** Använd Azure Policy för att varna och granska Azure-resurskonfigurationer. Principen kan användas för att identifiera vissa resurser som inte har konfigurerats med en privat slutpunkt.

När du använder Hybrid Runbook Worker kan du använda Azure Security Center för att utföra baslinjesökningar för dina virtuella Azure-datorer.  Ytterligare metoder för automatisk konfiguration är: Azure Automation State Configuration.

- [Så här åtgärdar du rekommendationer i Azure Security Center](../security-center/security-center-remediate-recommendations.md)

- [Komma igång med Azure Automation State Configuration](automation-dsc-getting-started.md)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy inbyggda exempel för Azure Automation](policy-reference.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminera oavsiktlig exponering av autentiseringsuppgifter

**Vägledning:** Implementera skanner för autentiseringsuppgifter för att identifiera autentiseringsuppgifter i koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault. 

- [Konfigurera skanner för autentiseringsuppgifter](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Prestandatest för Azure-säkerhet: Dataåterställning.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Säkerställ regelbundna automatiserade säkerhetskopieringar

**Vägledning:** Använd Azure Resource Manager för att distribuera Azure Automation-konton och relaterade resurser. Azure Resource Manager ger möjlighet att exportera mallar som kan användas som säkerhetskopior för att återställa Azure Automation-konton och relaterade resurser. Använd Azure Automation för att Azure Resource Manager api:et för mallexport regelbundet.

Använd funktionen för källkontrollsintegrering för att hålla dina runbooks i ditt Automation-konto uppdaterade med skript på lagringsplatsen för källkontroll.

- [Översikt över Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Azure Resource Manager mallreferens för Azure Automation resurser](/azure/templates/microsoft.automation/allversions)

- [Skapa ett Automation-konto med en Azure Resource Manager mall](quickstart-create-automation-account-template.md)

- [Export av en enskild resurs och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Resursgrupper – Exportera mall](/rest/api/resources/resourcegroups/exporttemplate)

- [Introduktion till Azure Automation](automation-intro.md)

- [Säkerhetskopiera nyckelvalvsnycklar i Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [Användning av kund hanterade nycklar för ett Automation-konto](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Använda källkontrollsintegrering](source-control-integration.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utför fullständiga systemsäkerhetskopior och säkerhetskopiera kund hanterade nycklar

**Vägledning:** Använd Azure Resource Manager för att distribuera Azure Automation-konton och relaterade resurser. Azure Resource Manager ger möjlighet att exportera mallar som kan användas som säkerhetskopior för att återställa Azure Automation-konton och relaterade resurser. Använd Azure Automation för att Azure Resource Manager api:et för mallexport regelbundet. Back up customer-managed keys within Azure Key Vault. Du kan exportera dina runbooks till skriptfiler med antingen Azure Portal eller PowerShell.

- [Översikt över Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Azure Resource Manager mallreferens för Azure Automation resurser](/azure/templates/microsoft.automation/allversions)

- [Skapa ett Automation-konto med en Azure Resource Manager mall](quickstart-create-automation-account-template.md)

- [Export av en enskild resurs och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Resursgrupper – Exportera mall](/rest/api/resources/resourcegroups/exporttemplate)

- [Introduktion till Azure Automation](automation-intro.md)

- [Säkerhetskopiera nyckelvalvsnycklar i Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [Användning av kund hanterade nycklar för ett Automation-konto](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Säkerhetskopiering av Azure-data för Automation-konton](./automation-managing-data.md#data-backup)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Verifiera alla säkerhetskopior, inklusive kund hanterade nycklar

**Vägledning:** Se till att du regelbundet kan distribuera Azure Resource Manager mallar till en isolerad prenumeration om det behövs. Testa återställning av säkerhetskopierade kund hanterade nycklar.

- [Distribuera resurser med ARM-mallar och Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

- [Så här återställer du nyckelvalvsnycklar i Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Användning av kund hanterade nycklar för ett Automation-konto](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Skydda säkerhetskopior och kund hanterade nycklar

**Vägledning:** Använd Azure DevOps för att lagra och hantera din kod på ett säkert sätt som Azure Resource Manager mallar. För att skydda resurser som du hanterar i Azure DevOps kan du bevilja eller neka behörigheter till specifika användare, inbyggda säkerhetsgrupper eller grupper som definierats i Azure Active Directory (Azure AD) om de är integrerade med Azure DevOps eller Active Directory om de är integrerade med TFS.

Använd funktionen för källkontrollsintegrering för att hålla dina runbooks i ditt Automation-konto uppdaterade med skript på lagringsplatsen för källkontroll.

- [Lagra kod i Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Om behörigheter och grupper i Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Använda källkontrollsintegrering](source-control-integration.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Skapa en guide för incidentsvar

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.

- [Vägledning om hur du skapar en egen process för svar på säkerhetsincidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center är en incidents uppbyggnad](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Kunden kan också använda NIST:s guide för hantering av säkerhetsincidenter för att underlätta skapandet av en egen plan för incidenthantering](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Skapa en procedur för incidentbedömning och prioritering

**Vägledning:** Security Center tilldelar en allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som ska undersökas först. Allvarlighetsgraden baseras på hur säker Security Center är i hitta eller analys som används för att utfärda aviseringen samt förtroendenivån för att det fanns skadlig avsikt bakom aktiviteten som ledde till aviseringen. 

Markera dessutom prenumerationer tydligt (t.ex. produktion, icke-produktion) med hjälp av taggar och skapa ett namngivningssystem för att tydligt identifiera och kategorisera Azure-resurser, särskilt de som bearbetar känsliga data.  Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="103-test-security-response-procedures"></a>10.3: Testa procedurer för säkerhetssvar

**Vägledning:** Genomför övningar för att regelbundet testa systemets incidentsvarsfunktioner för att skydda dina Azure-resurser. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [NIST:s publikation – Guide till test-, tränings- och övningsprogram för IT-planer och IT-funktioner](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Ange kontaktinformation om säkerhetsincidenter och konfigurera aviseringsmeddelanden för säkerhetsincidenter

**Vägledning:** Kontaktinformation om säkerhetsincident används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en obehörig eller obehörig part. Granska incidenter i efterhand för att se till att problemen är lösta.

- [Så här ställer du Azure Security Center säkerhetskontakt](../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Införliva säkerhetsaviseringar i systemet för incidentsvar

**Vägledning:** Exportera dina Azure Security Center och rekommendationer med hjälp av funktionen Kontinuerlig export för att identifiera risker för Azure-resurser. Med löpande export kan du exportera aviseringar och rekommendationer manuellt eller kontinuerligt. Du kan använda anslutningsappen Azure Security Center data för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du löpande export](../security-center/continuous-export.md)

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

**Vägledning:** Följ Microsofts regler för engagemang för att säkerställa att dina intrångstester inte bryter mot Microsofts principer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft.

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)
