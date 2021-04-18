---
title: Azure-säkerhetsbaslinje för Container Instances
description: Säkerhetsbaslinjen Container Instances innehåller procedurvägledning och resurser för implementering av säkerhetsrekommendationerna som anges i Azure Security Benchmark.
author: msmbaldwin
ms.service: container-instances
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8790e05edbaeb40debd997ea9b35d31b25947761
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598796"
---
# <a name="azure-security-baseline-for-container-instances"></a>Azure-säkerhetsbaslinje för Container Instances

Den här säkerhetsbaslinjen tillämpar riktlinjer [från Azure Security Benchmark version 1.0](../security/benchmarks/overview-v1.md) för att Container Instances. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure. Innehållet grupperas efter de säkerhetskontroller **som definierats** av Azure Security Benchmark och tillhörande vägledning som gäller för Container Instances. **Kontroller** som inte Container Instances eller för vilka ansvaret är Microsofts har undantagits.

Information om hur Container Instances mappning helt till Azure Security Benchmark finns i den fullständiga Container Instances [säkerhetsbaslinjemappningsfilen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Skydda Azure-resurser i virtuella nätverk

**Vägledning:** Integrera dina containergrupper i Azure Container Instances med ett virtuellt Azure-nätverk.  Med virtuella Azure-nätverk kan du placera många av dina Azure-resurser, till exempel containergrupper, i ett icke-Internet-dirigerbart nätverk.

Kontrollera utgående nätverksåtkomst från ett undernät som delegerats till Azure Container Instances med hjälp av Azure Firewall. 

- [Distribuera containerinstanser i ett virtuellt Azure-nätverk](/azure/container-instances/container-instances-vnet)

- [Så här distribuerar och konfigurerar du Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Övervaka och logga konfiguration och trafik för virtuella nätverk, undernät och nätverksgränssnitt

**Vägledning:** Använd Azure Security Center och följ nätverksskyddsrekommendationerna för att skydda dina nätverksresurser i Azure. Aktivera NSG-flödesloggar och skicka loggar till ett lagringskonto för trafikgranskning. Du kan också skicka NSG-flödesloggar till en Log Analytics-arbetsyta och använda Trafikanalys för att ge insikter om trafikflödet i ditt Azure-moln. Vissa fördelar med Trafikanalys är möjligheten att visualisera nätverksaktivitet och identifiera hotpunkter, identifiera säkerhetshot, förstå trafikflödesmönster och hitta felkonfigurationer i nätverk. 

- [Så här aktiverar du NSG-flödesloggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Aktivera och använda Trafikanalys](../network-watcher/traffic-analytics.md) 

- [Förstå nätverkssäkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="13-protect-critical-web-applications"></a>1.3: Skydda kritiska webbprogram

**Vägledning:** Inte tillämpligt. Benchmark är avsett för Azure App Service eller beräkningsresurser som är värdar för webbprogram.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

**Vägledning:** Aktivera DDoS Standard-skydd i dina virtuella nätverk för skydd mot DDoS-attacker. Använd Azure Security Center Integrated Threat Intelligence för att neka kommunikation med kända skadliga eller oanvända Internet-IP-adresser.  Distribuera Azure Firewall vid var och en av organisationens nätverksgränser med Hotinformation aktiverat och konfigurerat till "Avisering och neka" för skadlig nätverkstrafik.

Du kan använda Azure Security Center just-in-time-nätverksåtkomst för att konfigurera NSG:er för att begränsa exponering av slutpunkter till godkända IP-adresser under en begränsad period. Använd även Azure Security Center anpassningsbar nätverkshärdning för att rekommendera NSG-konfigurationer som begränsar portar och käll-IP-adresser baserat på faktisk trafik och hotinformation.

- [Så här konfigurerar du DDoS-skydd](/azure/virtual-network/manage-ddos-protection)

- [Så här distribuerar du Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Förstå Azure Security Center Integrated Threat Intelligence](../security-center/azure-defender.md)

- [Förstå Azure Security Center anpassningsbar nätverkshärdning](../security-center/security-center-adaptive-network-hardening.md)

- [Azure Security Center just-in-time-Access Control](../security-center/security-center-just-in-time.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="15-record-network-packets"></a>1.5: Registrera nätverkspaket

**Vägledning:** Om du använder ett molnbaserat privat register som Azure Container Registry med Azure Container Instances kan du aktivera flödesloggar för nätverkssäkerhetsgrupp (NSG) för den NSG som är kopplad till det undernät som används för att skydda ditt Azure-containerregister. Du kan registrera NSG-flödesloggarna till ett Azure Storage konto för att generera flödesposter. Om det krävs för att undersöka avvikande aktivitet aktiverar du Azure Network Watcher infångade paket.

- [Så här aktiverar du NSG-flödesloggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar du Network Watcher](../network-watcher/network-watcher-create.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuera nätverksbaserade system för intrångsidentifiering/intrångsskydd (IDS/IPS)

**Vägledning:** Välj ett erbjudande från den Azure Marketplace som stöder IDS/IPS-funktioner med funktioner för nyttolastgranskning. Om intrångsidentifiering och/eller skydd baserat på nyttolastgranskning inte är ett krav kan Azure Firewall med hotinformation användas. Azure Firewall filtrering baserad på hotinformation kan varna och neka trafik till och från kända skadliga IP-adresser och domäner. IP-adresserna och domänerna hämtas från Microsoft Threat Intelligence-flödet.

Distribuera valfri brandväggslösning vid var och en av organisationens nätverksgränser för att identifiera och/eller neka skadlig trafik.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Så här distribuerar du Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Så här konfigurerar du aviseringar med Azure Firewall](../firewall/threat-intel.md)

- [Distribuera i ett virtuellt nätverk – Azure Container Instances](container-instances-vnet.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Hantera trafik till webbprogram

**Vägledning:** Ej tillämpligt. Benchmark är avsett för webbprogram som körs på Azure App Service eller beräkningsresurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexiteten och det administrativa arbetet med nätverkssäkerhetsregler

**Vägledning:** Om du använder ett molnbaserat privat register som Azure Container Registry med Azure Container Instances för resurser som behöver åtkomst till containerregistret använder du tjänsttaggar för virtuellt nätverk för Azure Container Registry-tjänsten för att definiera nätverksåtkomstkontroller för nätverkssäkerhetsgrupper eller Azure Firewall. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttaggnamnet "AzureContainerRegistry" i rätt käll- eller målfält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras.

- [Tillåt åtkomst efter tjänsttagg](https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Underhålla standardsäkerhetskonfigurationer för nätverksenheter

**Vägledning:** När du använder Azure Container Registry med Azure Container Instances rekommenderar vi att du definierar och implementerar standardsäkerhetskonfigurationer för nätverksresurser som är associerade med ditt Azure-containerregister.

Använd Azure Policy alias i namnrymderna **Microsoft.ContainerRegistry** och **Microsoft.Network** för att skapa anpassade principer för att granska eller framtvinga nätverkskonfigurationen för dina containerregister.

Du kan använda Azure Blueprints för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljöartefakter, till exempel Azure Resource Manager-mallar, Azure RBAC-kontroller och principdefinitioner i en enda skissdefinition. Tillämpa enkelt skissen på nya prenumerationer och finjustera kontroll och hantering via versionshantering.

- [Granska efterlevnad för Azure-containerregister med hjälp av Azure Policy](../container-registry/container-registry-azure-policy.md)

- [Så här skapar du en Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentera trafikkonfigurationsregler

**Vägledning:** Kunden kan använda Azure Blueprints för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljöartefakter, till exempel Azure Resource Manager-mallar, Azure RBAC-kontroller och principer, i en enda skissdefinition. Tillämpa enkelt skissen på nya prenumerationer och finjustera kontroll och hantering via versionshantering.

- [Så här skapar du en Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

**Vägledning:** Använd Azure-aktivitetsloggen för att övervaka nätverksresurskonfigurationer och identifiera ändringar för nätverksresurser som är relaterade till dina containerregister. Skapa aviseringar i Azure Monitor som utlöses när ändringar av viktiga nätverksresurser sker.

- [Så här visar och hämtar du Händelser i Azure-aktivitetsloggen](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i Azure Security Benchmark: Logging and Monitoring ( [Prestandatest för Azure-säkerhet: Loggning och övervakning).](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera central hantering av säkerhetsloggar

**Vägledning:** Mata in loggar via Azure Monitor för att aggregera säkerhetsdata som genereras av en Azure-containerinstans. Inom Azure Monitor du en Log Analytics-arbetsyta för att fråga efter och utföra analyser och använda Azure Storage-konton för långsiktig lagring/arkiveringslagring.

- [Loggning av containergrupp och instans med Azure Monitor loggar](container-instances-log-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägledning:** Azure Monitor samlar in resursloggar (kallades tidigare diagnostikloggar) för användardrivna händelser. Samla in och använd dessa data för att granska autentiseringshändelser för containrar och tillhandahålla en fullständig aktivitetslogg för artefakter, till exempel pull- och push-händelser, så att du kan diagnostisera säkerhetsproblem med din containergrupp.

- [Loggning av containergrupp och instans med Azure Monitor loggar](container-instances-log-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera kvarhållning av säkerhetslogglagring

**Vägledning:** Inom Azure Monitor anger du kvarhållningsperioden för Log Analytics-arbetsytan enligt organisationens efterlevnadsregler. Använd Azure Storage-konton för långsiktig lagring/arkiveringslagring.

- [Så här anger du parametrar för loggbevarande för Log Analytics-arbetsytor](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning:** Analysera och övervaka Azure Container Instances loggar för avvikande beteende och granska regelbundet resultaten. Använd Azure Monitor Log Analytics-arbetsytan för att granska loggar och köra frågor på loggdata.

- [Förstå Log Analytics-arbetsyta](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Så här utför du anpassade frågor i Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

- [Så här skapar du en loggaktiverad containergrupp och frågeloggar](container-instances-log-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivera aviseringar för avvikande aktiviteter

**Vägledning:** Om du använder ett molnbaserat privat register som Azure Container Registry med Azure Container Instances använder du Azure Log Analytics-arbetsytan för att övervaka och varna om avvikande aktiviteter i säkerhetsloggar och händelser relaterade till ditt Azure-containerregister.

- [Azure Container Registry loggar för diagnostisk utvärdering och granskning](../container-registry/container-registry-diagnostics-audit-logs.md)

- [Så här varnar du om loggdata i Log Analytics](/azure/azure-monitor/learn/tutorial-response)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralisera loggning mot skadlig kod

**Vägledning:** Ej tillämpligt. Om du använder ett molnbaserat privat register som Azure-containerregister med Azure Container Instances, bearbetar eller producerar Inte Azure-containerregistret loggar relaterade till skadlig kod.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivera DNS-frågeloggning

**Vägledning:** Ej tillämpligt. Om du använder ett molnbaserat privat register som Azure-containerregister med Azure Container Instances är Azure-containerregistret en slutpunkt och initierar inte kommunikation och tjänsten frågar inte DNS.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Upprätthålla en förteckning över administrativa konton

**Vägledning:** Azure Active Directory (Azure AD) har inbyggda roller som måste tilldelas explicit och som kan frågas. Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

Om du använder ett molnbaserat privat register som Azure-containerregister med Azure Container Instances kan du för varje Azure-containerregister spåra om det inbyggda administratörskontot är aktiverat eller inaktiverat. Inaktivera kontot när det inte används.

- [Så här får du en katalogroll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Så här hämtar du medlemmar i en katalogroll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Azure Container Registry administratörskonto](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändra standardlösenord i förekommande fall

**Vägledning:** Azure Active Directory (Azure AD) har inte begreppet standardlösenord. Andra Azure-resurser som kräver ett lösenord tvingar fram att ett lösenord skapas med komplexitetskrav och en minsta längd på lösenord, som skiljer sig åt beroende på tjänsten. Du ansvarar för program från tredje part och Marketplace-tjänster som kan använda standardlösenord.

Om du använder ett molnbaserat privat register som Azure Container Registry med Azure Container Instances skapas och roteras automatiskt om standardadministratörskontot för ett Azure-containerregister är aktiverat. Inaktivera kontot när det inte används.

- [Azure Container Registry administratörskonto](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Använd dedikerade administrativa konton

**Vägledning:** Skapa standardprocedurer för användning av dedikerade administrativa konton. Använd Azure Security Center Identitets- och åtkomsthantering för att övervaka antalet administrativa konton.

Om du använder ett molnbaserat privat register som Azure Container Registry med Azure Container Instances skapar du procedurer för att aktivera det inbyggda administratörskontot för ett containerregister. Inaktivera kontot när det inte används.

- [Förstå Azure Security Center identitet och åtkomst](../security-center/security-center-identity-access.md)

- [Azure Container Registry administratörskonto](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory för enkel inloggning (SSO)

**Vägledning:** Använd vid behov Azure Active Directory enkel inloggning (Azure AD) i stället för att konfigurera enskilda fristående autentiseringsuppgifter per tjänst. Använd Azure Security Center rekommendationer för identitets- och åtkomsthantering.

Om du använder ett molnbaserat privat register som Azure Container Registry med Azure Container Instances kan du använda individuell inloggning som är integrerad med Azure AD för enskild åtkomst till containerregistret.

- [Förstå enkel inloggning med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Individuell inloggning till ett containerregister](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#individual-login-with-azure-ad)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använd multifaktorautentisering för Azure Active Directory åtkomstbaserad åtkomst

**Vägledning:** Aktivera Azure Active Directory (Azure AD) multifaktorautentisering och följ Azure Security Center rekommendationer för identitets- och åtkomsthantering.

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst inom Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Använd dedikerade datorer (arbetsstationer för privilegierad åtkomst) för alla administrativa uppgifter

**Vägledning:** Använd ARBETSSTATIONER (arbetsstationer för privilegierad åtkomst) med multifaktorautentisering konfigurerad för att logga in på och konfigurera Azure-resurser.

- [Läs mer om arbetsstationer för privilegierad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logga och avisering om misstänkta aktiviteter från administrativa konton

**Vägledning:** Använd Azure Active Directory (Azure AD) för generering av loggar och aviseringar när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure Security Center för att övervaka identitets- och åtkomstaktivitet.

- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här övervakar du användarnas identitets- och åtkomstrelaterade aktiviteter i Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Hantera Azure-resurser från endast godkända platser

**Vägledning:** Använd namngivna platser för villkorsstyrd åtkomst för att endast tillåta åtkomst från specifika logiska gruppningar av IP-adressintervall eller länder/regioner.

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning:** Använd Azure Active Directory (Azure AD) som centralt system för autentisering och auktorisering. Azure AD skyddar data med hjälp av stark kryptering för vilodata och under överföring. Azure AD saltar, hashar och lagrar användarautentiseringsuppgifter på ett säkert sätt.

- [Skapa och konfigurera en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Granska och stämma av användaråtkomst regelbundet

**Vägledning:** Azure Active Directory (Azure AD) innehåller loggar som hjälper dig att identifiera inaktuella konton. Använd dessutom Azure Identity Access Reviews för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Användaråtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

- [Förstå Azure AD-rapportering](/azure/active-directory/reports-monitoring/)

- [Så här använder du åtkomstgranskningar för Azure-identiteter](../active-directory/governance/access-reviews-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning:** Du har åtkomst till Azure Active Directory(Azure AD) inloggningsaktivitet, gransknings- och riskhändelseloggkällor, vilket gör att du kan integrera med val annat verktyg för säkerhetsinformation och händelsehantering (SIEM) /övervakning.

Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka granskningsloggar och inloggningsloggar till en Log Analytics-arbetsyta. Du kan konfigurera önskade aviseringar i Log Analytics-arbetsytan.

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Avisering om kontots beteendeavvikelse vid inloggning

**Vägledning:** Använd Azure Active Directory (Azure AD) Risk- och Identity Protection-funktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder relaterade till användaridentiteter.

- [Så här visar du riskfyllda inloggningar i Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du riskprinciper för Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ge Microsoft åtkomst till relevanta kunddata under supportscenarier

**Vägledning:** Inte tillgängligt; Customer Lockbox stöds inte för Azure Container Instances.

- [Lista över Customer Lockbox tjänster som stöds](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Upprätthålla en förteckning över känslig information

**Vägledning:** Använd resurstaggar för att spåra Azure-containerregister som lagrar eller bearbetar känslig information.

Tagga och versionscontaineravbildningar eller andra artefakter i ett register och lås avbildningar eller lagringslager för att spåra avbildningar som lagrar eller bearbetar känslig information.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

- [Rekommendationer för taggning och versionshantering av containeravbildningar](../container-registry/container-registry-image-tag-version.md)

- [Låsa en containeravbildning i ett Azure-containerregister](../container-registry/container-registry-image-lock.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolera system som lagrar eller bearbetar känslig information

**Vägledning:** Implementera separata containerregister, prenumerationer och/eller hanteringsgrupper för utveckling, testning och produktion. Resurser som lagrar eller bearbetar känsliga data bör vara tillräckligt isolerade.

Resurserna ska avgränsas med virtuella nätverk eller undernät, taggas på rätt sätt och skyddas av en nätverkssäkerhetsgrupp (NSG) eller Azure Firewall.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

- [Begränsa åtkomsten till ett Azure-containerregister med hjälp av ett virtuellt Azure-nätverk eller brandväggsregler](../container-registry/container-registry-vnet.md)

- [Så här skapar du en NSG med en säkerhetskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

- [Så här distribuerar du Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Så här konfigurerar du avisering eller avisering och nekar med Azure Firewall](../firewall/threat-intel.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Övervaka och blockera obehörig överföring av känslig information

**Vägledning:** Distribuera ett automatiserat verktyg i nätverks perimeterer som övervakar obehörig överföring av känslig information och blockerar sådana överföringar samtidigt som informationssäkerhetspersonal varnas.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft alla kunddata som känsliga och sträcker sig långt för att skydda mot förlust och exponering av kunddata. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och upprätthåller en uppsättning robusta dataskyddskontroller och -funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Kryptera all känslig information under överföring

**Vägledning:** Se till att klienter som ansluter till Azure Container Registry kan förhandla TLS 1.2 eller högre. Microsoft Azure TLS 1.2 förhandlas som standard.

Följ Azure Security Center rekommendationer för kryptering i vila och kryptering under överföring, om tillämpligt.

- [Förstå kryptering under överföring med Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Använd ett aktivt identifieringsverktyg för att identifiera känsliga data

**Vägledning:** Om du använder ett molnbaserat privat register som Azure Container Registry med Azure Container Instances är funktioner för dataidentifiering, klassificering och förlustskydd ännu inte tillgängliga för Azure Container Registry. Implementera en lösning från tredje part om det behövs i efterlevnadssyfte.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft alla kunddata som känsliga och sträcker sig långt för att skydda mot förlust och exponering av kunddata. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och upprätthåller en uppsättning robusta dataskyddskontroller och -funktioner.

- [Kryptera distributionsdata med Azure Container Instances](container-instances-encrypt-data.md)

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Använd rollbaserad åtkomstkontroll för att styra åtkomsten till resurser

**Vägledning:** Om du använder ett molnbaserat privat register som Azure Container Registry med Azure Container Instances använder du rollbaserad åtkomstkontroll i Azure (Azure RBAC) för att hantera åtkomst till data och resurser i ett Azure-containerregister.

- [Så här konfigurerar du Azure RBAC i Azure](../role-based-access-control/role-assignments-portal.md)

- [Azure Container Registry roller och behörigheter](../container-registry/container-registry-roles.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Använd värdbaserad dataförlustskydd för att framtvinga åtkomstkontroll

**Vägledning:** Om det krävs för kompatibilitet för beräkningsresurser implementerar du ett verktyg från tredje part, till exempel en automatiserad lösning för skydd mot dataförlust, för att framtvinga åtkomstkontroller för data även när data kopieras från ett system.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft alla kunddata som känsliga och går mycket långt för att skydda mot förlust och exponering av kunddata. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och upprätthåller en uppsättning robusta dataskyddskontroller och funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Kryptera känslig information i vila

**Vägledning:** Använda kryptering i vila på alla Azure-resurser. Om du använder ett molnbaserat privat register som Azure-containerregister med Azure Container Instances krypteras som standard alla data i ett Azure-containerregister i vila med hjälp av Microsoft-hanterade nycklar.

- [Förstå kryptering vid vila i Azure](../security/fundamentals/encryption-atrest.md)

- [Kund hanterade nycklar i Azure Container Registry](https://aka.ms/acr/cmk)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisering om ändringar av viktiga Azure-resurser

**Vägledning:** Log Analytics-arbetsytor är en central plats där du kan lagra och fråga loggdata, inte bara från Azure-resurser, utan även lokala resurser och resurser i andra moln. Azure Container Instances har inbyggt stöd för att skicka loggar och händelsedata till Azure Monitor loggar.

- [Containergrupps- och instansloggning med Azure Monitor loggar](container-instances-log-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Köra automatiserade sårbarhetsgenomsökningsverktyg

**Vägledning:** Dra nytta av lösningar för att genomsöka containeravbildningar i ett privat register och identifiera potentiella säkerhetsrisker. Det är viktigt att förstå djupet i hotidentifiering som de olika lösningarna ger. Följ rekommendationerna från Azure Security Center att utföra sårbarhetsbedömningar på dina containeravbildningar. Du kan också distribuera lösningar från tredje part från Azure Marketplace att utföra sårbarhetsbedömningar av avbildningar.

- [Säkerhetsrekommendationer för övervakning och genomsökning av containrar för Azure Container Instances](container-instances-image-security.md)

- [Azure Container Registry integrering med Security Center](/azure/security-center/azure-container-registry-integration)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Distribuera automatisk lösning för korrigeringshantering av operativsystem

**Vägledning:** Microsoft utför korrigeringshantering på de underliggande system som stöder körning av containrar.

Automatisera uppdateringar av containeravbildningar när uppdateringar av basavbildningar från operativsystemet och andra korrigeringar identifieras.

- [Om basavbildningsuppdateringar för Azure Container Registry uppgifter](../container-registry/container-registry-tasks-base-images.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Distribuera en automatiserad korrigeringshanteringslösning för programvarutitlar från tredje part

**Vägledning:** Du kan använda en lösning från tredje part för att korrigera programavbildningar. Om du använder ett molnbaserat privat register som Azure Container Registry med Azure Container Instances kan du också köra Azure Container Registry-uppgifter för att automatisera uppdateringar av programavbildningar i ett containerregister baserat på säkerhetskorrigeringar eller andra uppdateringar i basavbildningar.

- [Om basavbildningsuppdateringar för ACR-uppgifter](../container-registry/container-registry-tasks-base-images.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Jämför tillbaka-till-tillbaka-sårbarhetsgenomsökningar

**Vägledning:** Om du använder ett molnbaserat privat register som Azure Container Registry med Azure Container Instances integrerar du Azure Container Registry (ACR) med Azure Security Center för att möjliggöra regelbunden genomsökning av containeravbildningar efter säkerhetsrisker. Du kan också distribuera lösningar från tredje part från Azure Marketplace att utföra regelbundna genomsökningar av säkerhetsrisk för avbildningar.

- [Azure Container Registry integrering med Security Center](../security-center/defender-for-container-registries-introduction.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Använd en riskklassificeringsprocess för att prioritera åtgärder för identifierade säkerhetsrisker

**Vägledning:** Om du använder ett molnbaserat privat register som Azure Container Registry med Azure Container Instances integrerar du Azure Container Registry (ACR) med Azure Security Center för att möjliggöra regelbunden genomsökning av containeravbildningar efter sårbarheter och för att klassificera risker. Du kan också distribuera lösningar från tredje part från Azure Marketplace för att utföra regelbundna genomsökningar av bildrisker och riskklassificering.

- [Azure Container Registry integrering med Security Center](../security-center/defender-for-container-registries-introduction.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security Benchmark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Använd automatisk tillgångsidentifiering

**Vägledning:** Använd Azure Resource Graph att fråga efter/identifiera alla resurser (till exempel beräkning, lagring, nätverk, portar och protokoll osv.) i dina prenumerationer. Se till att du har rätt (läsbehörighet) i din klientorganisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via Resource Graph rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser i framtiden.

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="62-maintain-asset-metadata"></a>6.2: Underhålla tillgångsmetadata

**Vägledning:** Om du använder ett molnbaserat privat register som Azure Container Registry (ACR) med Azure Container Instances underhåller ACR metadata, inklusive taggar och manifest för avbildningar i ett register. Följ rekommenderade metoder för att tagga artefakter.

- [Om register, lagringsplatsen och avbildningar](../container-registry/container-registry-concepts.md)

- [Rekommendationer för taggning och versionshantering av containeravbildningar](../container-registry/container-registry-image-tag-version.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ta bort obehöriga Azure-resurser

**Vägledning:** Om du använder ett molnbaserat privat register som Azure Container Registry (ACR) med Azure Container Instances underhåller ACR metadata, inklusive taggar och manifest för avbildningar i ett register. Följ rekommenderade metoder för att tagga artefakter.

- [Om register, lagringsplatsen och avbildningar](../container-registry/container-registry-concepts.md)

- [Rekommendationer för taggning och versionshantering av containeravbildningar](../container-registry/container-registry-image-tag-version.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definiera och underhålla inventering av godkända Azure-resurser

**Vägledning:** Du måste skapa en inventering av godkända Azure-resurser enligt organisationens behov.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Övervaka för ej godkända Azure-resurser

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i dina prenumerationer.

Använd Azure Resource Graph för att fråga/identifiera resurser i sina prenumerationer. Se till att alla Azure-resurser som finns i miljön godkänns.

- [Granska efterlevnad för Azure-containerregister med hjälp av Azure Policy](../container-registry/container-registry-azure-policy.md)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Övervaka för program som inte är godkända i beräkningsresurser

**Vägledning:** Om du använder ett molnbaserat privat register som Azure Container Registry (ACR) med Azure Container Instances ska du analysera och övervaka Azure Container Registry-loggar för avvikande beteende och regelbundet granska resultaten. Använd Azure Monitor Log Analytics-arbetsytan för att granska loggar och köra frågor på loggdata.

- [Azure Container Registry loggar för diagnostisk utvärdering och granskning](../container-registry/container-registry-diagnostics-audit-logs.md)

- [Förstå Log Analytics-arbetsyta](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Så här utför du anpassade frågor i Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Ta bort ej godkända Azure-resurser och program

**Vägledning:** Azure Automation ger fullständig kontroll under distribution, drift och inaktivering av arbetsbelastningar och resurser. Du kan implementera en egen lösning för att ta bort obehöriga Azure-resurser. 

- [En introduktion till Azure Automation](../automation/automation-intro.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="68-use-only-approved-applications"></a>6.8: Använd endast godkända program

**Vägledning:** Ej tillämpligt. Benchmark är utformat för beräkningsresurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="69-use-only-approved-azure-services"></a>6.9: Använd endast godkända Azure-tjänster

**Vägledning:** Utnyttja Azure Policy för att begränsa vilka tjänster du kan etablera i din miljö.

- [Granska efterlevnad för Azure-containerregister med hjälp av Azure Policy](../container-registry/container-registry-azure-policy.md)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en viss resurstyp med Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Upprätthålla en förteckning över godkända programvarutitlar

**Vägledning:** Inte tillämpligt. Benchmark är utformat för beräkningsresurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning:** Använd operativsystemspecifika konfigurationer eller resurser från tredje part för att begränsa användarnas möjlighet att köra skript i Azure-beräkningsresurser.

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resources Manager](../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Begränsa användarnas möjlighet att köra skript inom beräkningsresurser

**Vägledning:** Använd operativsystemspecifika konfigurationer eller resurser från tredje part för att begränsa användarnas möjlighet att köra skript i Azure-beräkningsresurser.

- [Till exempel hur du styr PowerShell-skriptkörning i Windows-miljöer](/powershell/module/microsoft.powershell.security/set-executionpolicy)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fysiskt eller logiskt avsegrera program med hög risk

**Vägledning:** Programvara som krävs för verksamhetsdrift, men kan medföra högre risk för organisationen, bör isoleras inom sin egen virtuella dator och/eller virtuella nätverk och vara tillräckligt skyddad med antingen en Azure Firewall eller en nätverkssäkerhetsgrupp.

- [Så här skapar du ett virtuellt nätverk](../virtual-network/quick-create-portal.md)

- [Så här skapar du en NSG med en säkerhetskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security Benchmark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning:** Använd Azure Policy eller Azure Security Center för att underhålla säkerhetskonfigurationer för alla Azure-resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Granska efterlevnad för Azure-containerregister med hjälp av Azure Policy](../container-registry/container-registry-azure-policy.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Upprätta säkra operativsystemkonfigurationer

**Vägledning:** Använd Azure Security Center rekommendationen "Åtgärda sårbarheter i säkerhetskonfigurationer på din Virtual Machines" för att upprätthålla säkerhetskonfigurationer på alla beräkningsresurser.

- [Så här övervakar du Azure Security Center rekommendationer](../security-center/security-center-recommendations.md)

- [Så här åtgärdar du Azure Security Center rekommendationer](../security-center/security-center-remediate-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Upprätthålla säkra Azure-resurskonfigurationer

**Vägledning:** Använd Effekterna av Azure-principen [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar för alla dina Azure-resurser.

Om du använder ett molnbaserat privat register som Azure Container Registry (ACR) med Azure Container Instances kan du granska efterlevnaden av Azure-containerregister med hjälp Azure Policy:.

- [Granska efterlevnad för Azure-containerregister med hjälp av Azure Policy](../container-registry/container-registry-azure-policy.md)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Förstå Azure Policy effekter](../governance/policy/concepts/effects.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Upprätthåll säkra operativsystemkonfigurationer

**Vägledning:** Inte tillämpligt; den här kontrollen är avsedd för beräkningsresurser.

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Lagra konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning:** Om du använder anpassade Azure-principdefinitioner använder du Azure Repos för att lagra och hantera din kod på ett säkert sätt.

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Dokumentation om Azure Repos](/azure/devops/repos/)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Lagra anpassade operativsystemavbildningar på ett säkert sätt

**Vägledning:** Inte tillämpligt; den här kontrollen gäller endast för beräkningsresurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuera konfigurationshanteringsverktyg för Azure-resurser

**Vägledning:** Använd Azure Policy för att avisering, granskning och framtvinga systemkonfigurationer. Utveckla dessutom en process och pipeline för hantering av principundantag.

Om du använder ett molnbaserat privat register som Azure Container Registry (ACR) med Azure Container Instances bör du granska efterlevnaden av Azure-containerregister med hjälp Azure Policy:.

- [Granska efterlevnad för Azure-containerregister med hjälp av Azure Policy](../container-registry/container-registry-azure-policy.md)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Förstå Azure Policy effekter](../governance/policy/concepts/effects.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Distribuera konfigurationshanteringsverktyg för operativsystem

**Vägledning:** Ej tillämpligt. Benchmark gäller för beräkningsresurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementera automatiserad konfigurationsövervakning för Azure-resurser

**Vägledning:** Använd Azure Security Center för att utföra baslinjesökningar för dina Azure-resurser. 

Tillämpa Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i dina prenumerationer.

Om du använder ett molnbaserat privat register som Azure Container Registry (ACR) med Azure Container Instances kan du granska efterlevnaden av Azure-containerregister med hjälp Azure Policy:.

- [Så här åtgärdar du rekommendationer i Azure Security Center](../security-center/security-center-remediate-recommendations.md)

- [Granska efterlevnad för Azure-containerregister med hjälp av Azure Policy](../container-registry/container-registry-azure-policy.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementera automatisk konfigurationsövervakning för operativsystem

**Vägledning:** Använd Azure Security Center för att utföra baslinjesökningar för OS- och Docker-inställningar för containrar. 

- [Förstå rekommendationer för Azure Security Center-container](../security-center/container-security.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning:** Använd hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda hemlighetshantering för dina molnprogram.

- [Så här integrerar du med azure-hanterade identiteter](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Så här skapar du en Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Så här autentiserar du till Key Vault](container-instances-managed-identity.md)

- [Tilldela en Key Vault åtkomstprincip](../key-vault/general/assign-access-policy-portal.md)

- [Använda hanterade identiteter med Azure Container Instances](../container-registry/container-registry-tasks-authentication-managed-identity.md)

- [Så här krypterar du data med Container Instances](container-instances-encrypt-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Hantera identiteter på ett säkert och automatiskt sätt

**Vägledning:** Använda hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet Azure Active Directory (Azure AD). Med hanterade identiteter kan du autentisera till alla tjänster som stöder Azure AD-autentisering, Azure Key Vault, utan några autentiseringsuppgifter i koden.

Om du använder ett molnbaserat privat register som Azure Container Registry (ACR) med Azure Container Instances kan du granska efterlevnaden av Azure-containerregister med hjälp Azure Policy:.

- [Granska efterlevnad för Azure-containerregister med hjälp av Azure Policy](../container-registry/container-registry-azure-policy.md)

- [Så här konfigurerar du hanterade identiteter](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Använda hanterade identiteter med Azure Container Instances](container-instances-managed-identity.md)

- [Använda en hanterad azure-identitet för att autentisera till ett Azure-containerregister](../container-registry/container-registry-authentication-managed-identity.md)

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

**Vägledning:** Använd Microsoft Antimalware för Azure Cloud Services och Virtual Machines att kontinuerligt övervaka och skydda dina resurser. För Linux använder du en lösning mot skadlig programvara från tredje part.

- [Så här konfigurerar du Microsoft Antimalware för Cloud Services och Virtual Machines](../security/fundamentals/antimalware.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Skanna filer i förväg som ska laddas upp till Icke-beräkningsbaserade Azure-resurser

**Vägledning:** Microsoft Antimalware på den underliggande värden som stöder Azure-tjänster (till exempel Azure Container Instances), men den körs inte på kunddata.

Försök alla filer som laddas upp till Icke-beräkningsbaserade Azure-resurser, till exempel App Service, Data Lake Storage, Blob Storage osv.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Prestandatest för Azure-säkerhet: Dataåterställning.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Säkerställ regelbundna automatiserade säkerhetskopieringar

**Vägledning:** Om du använder ett molnbaserat privat register som Azure Container Registry (ACR) med Azure Container Instances replikeras alltid data i ditt Microsoft Azure-containerregister automatiskt för att säkerställa hållbarhet och hög tillgänglighet. Azure Container Registry dina data så att de skyddas från planerade och oplanerade händelser.

Du kan också geo-replikera ett containerregister för att underhålla registerrepliker i flera Azure-regioner.

- [Geo-replikering i Azure Container Registry](../container-registry/container-registry-geo-replication.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utför fullständiga systemsäkerhetskopior och säkerhetskopiera kund hanterade nycklar

**Vägledning:** Om du vill kan du registera containeravbildningar genom att importera från ett register till ett annat.

Back up customer-managed keys in Azure Key Vault using Azure command-line tools or SDKs.

- [Importera containeravbildningar till ett containerregister](../container-registry/container-registry-import-images.md)

- [Säkerhetskopiera nyckelvalvsnycklar i Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [Kryptera distributionsdata med Container Instances](container-instances-encrypt-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Verifiera alla säkerhetskopior, inklusive kund hanterade nycklar

**Vägledning:** Testa återställning av säkerhetskopierade kundhanteringsnycklar i Azure Key Vault azure-kommandoradsverktyg eller -SDK:er.

- [Återställa Azure Key Vault i Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Skydda säkerhetskopior och kund hanterade nycklar

**Vägledning:** Du kan aktivera Soft-Delete i Azure Key Vault att skydda nycklar mot oavsiktlig eller skadlig borttagning.

- [Så här aktiverar du Soft-Delete i Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Skapa en incidentsvarsguide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.

- [Så här konfigurerar du arbetsflödesautomation inom Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Vägledning om hur du skapar en egen process för svar på säkerhetsincidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center är en incidents uppbyggnad](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [NIST:s guide för hantering av säkerhetsincidenter](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Skapa en procedur för incidentbedömning och prioritering

**Vägledning:** Azure Security Center tilldelar en allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som ska undersökas först. Allvarlighetsgraden baseras på hur säker Security Center är vid upptäckten eller analysmetoden som användes för att utfärda aviseringen samt konfidensnivån för att det fanns en skadlig avsikt bakom aktiviteten som ledde till aviseringen.

Markera dessutom prenumerationer med hjälp av taggar och skapa ett namngivningssystem för att identifiera och kategorisera Azure-resurser, särskilt de som bearbetar känsliga data.  Det är ditt ansvar att prioritera åtgärder för aviseringar baserat på allvarligheten i de Azure-resurser och den miljö där incidenten inträffade. 

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [Använda taggar för att organisera dina Azure-resurser](/azure/azure-resource-manager/resource-group-using-tags)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="103-test-security-response-procedures"></a>10.3: Testa procedurer för säkerhetssvar

**Vägledning:** Genomför övningar för att regelbundet testa systemets funktioner för incidenter. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [NIST:s publikation – Guide till test-, tränings- och övningsprogram för IT-planer och IT-funktioner](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Ange kontaktuppgifter för säkerhetsincidenter och konfigurera aviseringsmeddelanden för säkerhetsincidenter

**Vägledning:** Kontaktinformation om säkerhetsincident används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har använts av en obehörig part. Granska incidenter i efterhand för att säkerställa att problemen är lösta.

- [Konfigurera en säkerhetskontakt i Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Införliva säkerhetsaviseringar i systemet för incidenter

**Vägledning:** Exportera dina Azure Security Center och rekommendationer med hjälp av funktionen Kontinuerlig export. Med löpande export kan du exportera aviseringar och rekommendationer manuellt eller kontinuerligt. Du kan använda anslutningsappen Azure Security Center data för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisera svaret på säkerhetsaviseringar

**Vägledning:** Använd funktionen Arbetsflödesautomation i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" för säkerhetsaviseringar och rekommendationer.

- [Så här konfigurerar du arbetsflödesautomation och Logic Apps](../security-center/workflow-automation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [Azure Security Benchmark: Penetration Tests (Intrångstester) och Red Team Exercises (Red Team-övningar).](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Genomför regelbundna intrångstester av dina Azure-resurser och se till att alla viktiga säkerhetsresultat åtgärdas

**Vägledning:** Följ Microsoft Cloud Penetration Testing Rules of Engagement för att säkerställa att dina intrångstester inte bryter mot Microsofts principer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft. 

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
