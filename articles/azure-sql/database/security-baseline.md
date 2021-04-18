---
title: Azure-säkerhetsbaslinje för Azure SQL Database
description: Den Azure SQL Database säkerhetsbaslinjen innehåller procedurvägledning och resurser för att implementera säkerhetsrekommendationerna som anges i Azure Security Benchmark.
author: msmbaldwin
ms.service: sql-database
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 29db5b82d73bf96465581ccd6a663455464bbeb9
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599578"
---
# <a name="azure-security-baseline-for-azure-sql-database"></a>Azure-säkerhetsbaslinje för Azure SQL Database

Den här säkerhetsbaslinjen tillämpar riktlinjer [från Azure Security Benchmark version 1.0](../../security/benchmarks/overview-v1.md) för Azure SQL Database. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure. Innehållet grupperas efter de säkerhetskontroller **som definierats** av Azure Security Benchmark och tillhörande vägledning som gäller för Azure SQL Database. **Kontroller** som inte Azure SQL Database, eller för vilka ansvaret är Microsofts, har undantagits. Information om hur Azure SQL Database mappning helt till Azure Security Benchmark finns i den fullständiga Azure Monitor [säkerhetsbaslinjemappningsfilen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Skydda Azure-resurser i virtuella nätverk

**Vägledning:** Du kan aktivera Azure Private Link för att tillåta åtkomst till Azure PaaS-tjänster (till exempel SQL Database) och Azure-värdtjänster för kunder/partner via en privat slutpunkt i ditt virtuella nätverk. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet. 

Använd SQL-tjänsttaggarna för att tillåta utgående trafik via nätverkssäkerhetsgrupper för att tillåta att trafik når Azure SQL Database nätverk.

Regler för virtuellt nätverk gör Azure SQL Database att endast acceptera kommunikation som skickas från valda undernät i ett virtuellt nätverk.

- [Konfigurera en Private Link för Azure SQL Database](/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database)

- [Så här använder du tjänstslutpunkter för virtuellt nätverk och regler för databasservrar](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Övervaka och logga konfiguration och trafik för virtuella nätverk, undernät och nätverksgränssnitt

**Vägledning:** Använd Azure Security Center och åtgärda rekommendationer för nätverksskydd för det undernät Azure SQL Database server distribueras till. 

För Azure Virtual Machines (VM) som ska ansluta till din Azure SQL Database Server-instans aktiverar du flödesloggar för nätverkssäkerhetsgrupp (NSG) för de NSG:er som skyddar de virtuella datorerna och skickar loggar till ett Azure Storage-konto för trafikgranskning. 

Du kan också skicka NSG-flödesloggar till en Log Analytics-arbetsyta och använda Trafikanalys för att ge insikter om trafikflödet i ditt Azure-moln. Vissa fördelar med Trafikanalys är möjligheten att visualisera nätverksaktivitet och identifiera hotpunkter, identifiera säkerhetshot, förstå trafikflödesmönster och hitta felkonfigurationer i nätverk.

- [Så här aktiverar du NSG-flödesloggar](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Förstå nätverkssäkerhet som tillhandahålls av Azure Security Center](../../security-center/security-center-network-recommendations.md)

- [Aktivera och använda Trafikanalys](../../network-watcher/traffic-analytics.md)

- [Förstå nätverkssäkerhet som tillhandahålls av Azure Security Center](../../security-center/security-center-network-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

**Vägledning:** Aktivera DDoS Protection Standard på de virtuella nätverk som är associerade med dina SQL Server-instanser för skydd mot distribuerade DoS-attacker. Använd Azure Security Center Integrated Threat Intelligence för att neka kommunikation med kända skadliga eller oanvända Internet-IP-adresser.

- [Så här konfigurerar du DDoS-skydd](/azure/virtual-network/manage-ddos-protection)

- [Förstå Azure Security Center Integrated Threat Intelligence](/azure/security-center/security-center-alerts-data-services)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="15-record-network-packets"></a>1.5: Registrera nätverkspaket

**Vägledning:** För Azure Virtual Machines (VM) som ska ansluta till din Azure SQL Database-instans aktiverar du flödesloggar för nätverkssäkerhetsgrupp (NSG) för de NSG:er som skyddar de virtuella datorerna och skickar loggar till ett Azure Storage-konto för trafikgranskning. Om det krävs för att undersöka avvikande aktivitet aktiverar du Network Watcher infångade paket.

- [Så här aktiverar du NSG-flödesloggar](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar du Network Watcher](../../network-watcher/network-watcher-create.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuera nätverksbaserade system för intrångsidentifiering/intrångsskydd (IDS/IPS)

**Vägledning:** Aktivera Advanced Threat Protection (ATP) för Azure SQL Database.  Användarna får en avisering om misstänkta databasaktiviteter, potentiella sårbarheter och SQL- injectionsattacker, samt avvikande databasåtkomst och frågemönster. Advanced Threat Protection integrerar även aviseringar med Azure Security Center. 

- [Förstå och använda Advanced Threat Protection för Azure SQL Database](/azure/sql-database/sql-database-threat-detection-overview)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexitet och administrativa kostnader för nätverkssäkerhetsregler

**Vägledning:** Använda tjänsttaggar för virtuella nätverk för att definiera nätverksåtkomstkontroller för nätverkssäkerhetsgrupper eller Azure Firewall. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttaggnamnet (t.ex. ApiManagement) i lämpligt käll- eller målfält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras.

När du använder tjänstslutpunkter för Azure SQL Database krävs utgående till Azure SQL Database offentliga IP-adresser: Nätverkssäkerhetsgrupper (NSG: er) måste öppnas för att Azure SQL Database IP-adresser för att tillåta anslutning. Du kan göra detta med hjälp av NSG-tjänsttaggar för Azure SQL Database.

- [Förstå tjänsttaggar med tjänstslutpunkter för Azure SQL Database](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations)

- [Förstå och använda tjänsttaggar](../../virtual-network/service-tags-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Underhålla standardsäkerhetskonfigurationer för nätverksenheter

**Vägledning:** Definiera och implementera nätverkssäkerhetskonfigurationer för dina Azure SQL Database-serverinstanser med Azure Policy. Du kan använda namnområdet "Microsoft.Sql" för att definiera anpassade principdefinitioner eller använda någon av de inbyggda principdefinitionerna som utformats för Azure SQL Database servernätverksskydd. Ett exempel på en tillämplig inbyggd nätverkssäkerhetsprincip för Azure SQL Database server är: "SQL Server ska använda en tjänstslutpunkt för virtuellt nätverk".

 

Använd Azure Blueprints för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljöartefakter, till exempel Azure Resource Management-mallar, rollbaserad åtkomstkontroll (Azure RBAC) och principer i en enda skissdefinition. Tillämpa enkelt skissen på nya prenumerationer och miljöer och finjustera kontroll och hantering via versionshantering.

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du en Azure Blueprint](../../governance/blueprints/create-blueprint-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentera trafikkonfigurationsregler

**Vägledning:** Använd taggar för nätverkssäkerhetsgrupper (NSG) och andra resurser som rör nätverkssäkerhet och trafikflöde. För enskilda NSG-regler använder du fältet "Beskrivning" för att ange affärs behov och/eller varaktighet (osv.) för regler som tillåter trafik till/från ett nätverk.

Använd någon av de inbyggda Azure Policy-definitioner som rör taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och för att meddela dig om befintliga otaggade resurser.

Du kan använda Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

- [Skapa och använda taggar](/azure/azure-resource-manager/resource-group-using-tags)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

**Vägledning:** Använd Azure-aktivitetsloggen för att övervaka nätverksresurskonfigurationer och identifiera ändringar för nätverksresurser relaterade till dina Azure SQL Database-serverinstanser. Skapa aviseringar i Azure Monitor som utlöses när ändringar av viktiga nätverksresurser sker.

- [Så här visar och hämtar du Händelser i Azure-aktivitetsloggen](/azure/azure-monitor/platform/activity-log-view)

- [Så här skapar du aviseringar i Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [Prestandatest för Azure-säkerhet: Loggning och övervakning.](../../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera central hantering av säkerhetsloggar

**Vägledning:** Aktivera granskning för Azure SQL Database att spåra databashändelser och skriva dem till en granskningslogg i ditt Azure Storage-konto, Log Analytics-arbetsyta eller Event Hubs.

Dessutom kan du strömma Azure SQL diagnostiktelemetri till Azure SQL-analys, en molnlösning som övervakar prestanda för Azure SQL-databaser och Azure SQL-hanterade instanser i stor skala och över flera prenumerationer. Den kan hjälpa dig att samla Azure SQL Database och visualisera prestandamått och den har inbyggd intelligens för prestandafelsökning.

- [Konfigurera granskning för Azure SQL Database](/azure/sql-database/sql-database-auditing)

- [Samla in plattformsloggar och mått med Azure Monitor](/azure/sql-database/sql-database-metrics-diag-logging)

- [Så här strömmar du diagnostik till Azure SQL-analys](/azure/sql-database/sql-database-metrics-diag-logging#stream-into-azure-sql-analytics)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägledning:** Aktivera granskning på Azure SQL Database-serverinstansen och välj en lagringsplats för granskningsloggarna (Azure Storage, Log Analytics eller Event Hub).

- [Så här aktiverar du granskning för Azure SQL Server](/azure/sql-database/sql-database-auditing)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.3](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera kvarhållning av säkerhetslogglagring

**Vägledning:** När du lagrar Azure SQL Database loggar på en Log Analytics-arbetsyta ska du ange kvarhållningsperiod för loggar enligt organisationens efterlevnadsregler.

- [Så här anger du parametrar för loggbevarande](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.5](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-5.md)]

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning:** Analysera och övervaka loggar för avvikande beteenden och granska regelbundet resultaten. Använd Azure Security Center Advanced Threat Protection för att varna om ovanliga aktiviteter relaterade till din Azure SQL Database instans. Du kan också konfigurera aviseringar baserat på måttvärden eller Azure-aktivitetsloggposter relaterade till dina Azure SQL Database instanser.

- [Förstå Advanced Threat Protection och aviseringar för Azure SQL Server](/azure/sql-database/sql-database-threat-detection-overview)

- [Så här konfigurerar du anpassade aviseringar för Azure SQL Database](alerts-insights-configure-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivera aviseringar för avvikande aktiviteter

**Vägledning:** Använd Azure Security Center Advanced Threat Protection för Azure SQL-databaser för övervakning och avisering om avvikande aktivitet. Aktivera Azure Defender sql för dina SQL-databaser. Azure Defender för SQL innehåller funktioner för att identifiera och klassificera känsliga data, visa och åtgärda potentiella säkerhetsrisker i databasen samt identifiera avvikande aktiviteter som kan tyda på ett hot mot databasen.

- [Förstå Advanced Threat Protection och aviseringar för Azure SQL Database](/azure/sql-database/sql-database-threat-detection-overview)

- [Så här aktiverar du Azure Defender för SQL för Azure SQL Database](azure-defender-for-sql.md)

- [Hantera aviseringar i Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.7](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-7.md)]

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security Benchmark: Identity and Access Control](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Upprätthålla en förteckning över administrativa konton

**Vägledning:** Azure Active Directory (Azure AD) har inbyggda roller som måste tilldelas explicit och som kan frågas. Använd Azure AD PowerShell-modulen för att köra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

- [Så här får du en katalogroll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Så här hämtar du medlemmar i en katalogroll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändra standardlösenord i förekommande fall

**Vägledning:** Azure Active Directory (Azure AD) har inte begreppet standardlösenord. När du etablerar Azure SQL Database instans rekommenderar vi att du integrerar autentisering med Azure AD.

- [Så här konfigurerar och hanterar du Azure AD-autentisering med Azure SQL](/azure/azure-sql/database/authentication-aad-configure)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Använd dedikerade administrativa konton

**Vägledning:** Skapa principer och procedurer kring användningen av dedikerade administrativa konton. Använd Azure Security Center identitets- och åtkomsthantering för att övervaka antalet administrativa konton.

- [Förstå Azure Security Center identitet och åtkomst](../../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använd multifaktorautentisering för Azure Active Directory åtkomstbaserad åtkomst

**Vägledning:** Aktivera Azure Active Directory (Azure AD) multifaktorautentisering och följ Azure Security Center rekommendationer för identitets- och åtkomsthantering.

- [Så här aktiverar du multifaktorautentisering i Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst inom Azure Security Center](../../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Använd säkra, Azure-hanterade arbetsstationer för administrativa uppgifter

**Vägledning:** Använd en PAW (Privileged Access Workstation) med multifaktorautentisering som konfigurerats för att logga in på och konfigurera Azure-resurser.

- [Läs mer om arbetsstationer med privilegierad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Så här aktiverar du multifaktorautentisering i Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logga och avisering om misstänkta aktiviteter från administrativa konton

**Vägledning:** Använd Azure Active Directory (Azure AD) för generering av loggar och aviseringar när misstänkt eller osäker aktivitet inträffar i miljön.

Använd Advanced Threat Protection för Azure SQL Database att identifiera avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Övervaka användarnas identitets- och åtkomstaktivitet i Azure Security Center](../../security-center/security-center-identity-access.md)

- [Granska Advanced Threat Protection och potentiella aviseringar](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview#alerts)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Hantera Azure-resurser från endast godkända platser

**Vägledning:** Använd villkorsstyrd åtkomst med namngivna platser för att tillåta portal- och Azure Resource Management-åtkomst från endast specifika logiska grupper av IP-adressintervall eller länder/regioner.

- [Så här konfigurerar du namngivna platser i Azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning:** Skapa en Azure Active Directory (Azure AD)-administratör för dina Azure SQL Database-serverinstanser.

- [Så här konfigurerar och hanterar du Azure AD-autentisering med Azure SQL](authentication-aad-configure.md)

- [Skapa och konfigurera en Azure AD-instans](../../active-directory-domain-services/tutorial-create-instance.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 3.9](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Granska och stämma av användaråtkomst regelbundet

**Vägledning:** Azure Active Directory (Azure AD) innehåller loggar som hjälper dig att identifiera inaktuella konton. Dessutom kan du använda Azure Identity-åtkomstgranskningar för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Användarnas åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

- [Så här använder du Azure Identity Access Reviews](../../active-directory/governance/access-reviews-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning:** Konfigurera Azure Active Directory-autentisering (Azure AD) med Azure SQL och skapa diagnostikinställningar för Azure AD-användarkonton, skicka granskningsloggar och inloggningsloggar till en Log Analytics-arbetsyta. Konfigurera önskade aviseringar i Log Analytics-arbetsytan.

- [Så här konfigurerar och hanterar du Azure AD-autentisering med Azure SQL](authentication-aad-configure.md)

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Avisering om kontots beteendeavvikelse vid inloggning

**Vägledning:** Använd Azure Active Directory (Azure AD) Identity Protection och riskidentifiering för att konfigurera automatiserade svar på identifierade misstänkta åtgärder relaterade till användaridentiteter. Dessutom kan du mata in data i Azure Sentinel för ytterligare undersökning.

- [Så här visar du azure AD-risk inloggningar](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Så här konfigurerar och aktiverar du riskprinciper för Identity Protection](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ge Microsoft åtkomst till relevanta kunddata under supportscenarier

**Vägledning:** I supportscenarier där Microsoft behöver åtkomst till kunddata tillhandahåller Azure Customer Lockbox ett gränssnitt där kunder kan granska och godkänna eller avvisa begäranden om kunddataåtkomst.

- [Förstå Customer Lockbox](../../security/fundamentals/customer-lockbox-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Upprätthålla en förteckning över känslig information

**Vägledning:** Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information.

- [Skapa och använda taggar](/azure/azure-resource-manager/resource-group-using-tags)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-1.md)]

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolera system som lagrar eller bearbetar känslig information

**Vägledning:** Implementera separata prenumerationer och/eller hanteringsgrupper för utveckling, testning och produktion. Resurserna ska avgränsas med Vnet/undernät, taggas på rätt sätt och skyddas i en NSG eller Azure Firewall. Resurser som lagrar eller bearbetar känsliga data bör isoleras. Använd Private Link; distribuera Azure SQL Server i ditt virtuella nätverk och anslut privat med privata slutpunkter.

- [Så här skapar du ytterligare Azure-prenumerationer](/azure/billing/billing-create-subscription)

- [Så här skapar du Hanteringsgrupper](/azure/governance/management-groups/create)

- [Skapa och använda taggar](/azure/azure-resource-manager/resource-group-using-tags)

- [Konfigurera en Private Link för Azure SQL Database](/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Övervaka och blockera obehörig överföring av känslig information

**Vägledning:** För databaser i Azure SQL Database lagrar eller bearbetar känslig information markerar du databasen och relaterade resurser som känsliga med hjälp av Taggar. Konfigurera Private Link tillsammans med tjänsttaggar för nätverkssäkerhetsgrupp på dina Azure SQL Database instanser för att förhindra exfiltrering av känslig information.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt och går mycket långt för att skydda mot förlust och exponering av kunddata. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och upprätthåller en uppsättning robusta dataskyddskontroller och funktioner.

- [Så här konfigurerar Private Link och NSG:er för att förhindra data exfiltrering på Azure SQL Database instanser](/azure/sql-database/sql-database-private-endpoint-overview)

- [Förstå skydd av kunddata i Azure](../../security/fundamentals/protection-customer-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Använd ett aktivt identifieringsverktyg för att identifiera känsliga data

**Vägledning:** Använd Azure SQL Database för dataidentifiering och -klassificering. Dataidentifiering och -klassificering ger avancerade funktioner som är inbyggda Azure SQL Database för identifiering, klassificering och märkning som skyddar &amp; känsliga data i dina databaser.

- [Använda dataidentifiering och -klassificering för Azure SQL Server](/azure/sql-database/sql-database-data-discovery-and-classification)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.5](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-5.md)]

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Använd rollbaserad åtkomstkontroll för att styra åtkomsten till resurser

**Vägledning:** Använd Azure Active Directory (Azure AD) för autentisering och kontroll av åtkomst Azure SQL Database instanser.

- [Integrera Azure SQL Server med Azure AD för autentisering](/azure/sql-database/sql-database-aad-authentication)

- [Så här styr du åtkomst i Azure SQL Server](/azure/sql-database/sql-database-control-access)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Kryptera känslig information i vila

**Vägledning:** Transparent datakryptering (TDE) hjälper till att skydda Azure SQL Database, Azure SQL managed instance och Azure Data Warehouse mot hot om skadlig offlineaktivitet genom att kryptera vilodata. TDE utför realtidskryptering och realtidsdekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler i vila, utan att några ändringar krävs i programmet. Som standard är TDE aktiverat för alla nyligen distribuerade databaser i SQL Database och SQL Managed Instance. TDE-krypteringsnyckeln kan hanteras av antingen Microsoft eller kunden.

- [Hantera transparent datakryptering och använda egna krypteringsnycklar](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.8](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisering om ändringar av viktiga Azure-resurser

**Vägledning:** Använd Azure Monitor med Azure-aktivitetsloggen för att skapa aviseringar för när ändringar sker i produktionsinstanser av Azure SQL Database och andra kritiska eller relaterade resurser.

- [Så här skapar du aviseringar för Azure-aktivitetslogghändelser](/azure/azure-monitor/platform/alerts-activity-log)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security Benchmark: Vulnerability Management](../../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Kör automatiserade sårbarhetsgenomsökningsverktyg

**Vägledning:** Aktivera Azure Defender för SQL för Azure SQL Database och följ rekommendationerna från Azure Security Center om att utföra sårbarhetsbedömningar på dina Azure SQL-servrar.

- [Så här kör du sårbarhetsbedömningar på Azure SQL Database](/azure/sql-database/sql-vulnerability-assessment)

- [Så här aktiverar du Azure Defender för SQL](azure-defender-for-sql.md)

- [Så här implementerar du Azure Security Center rekommendationer för sårbarhetsbedömning](/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-1.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Jämför tillbaka-till-tillbaka-sårbarhetsgenomsökningar

**Vägledning:** Aktivera periodiska återkommande genomsökningar för Azure SQL Database instanser; Detta konfigurerar en sårbarhetsbedömning för att automatiskt köra en genomsökning av databasen en gång i veckan. En sammanfattning av genomsökningsresultatet skickas till de e-postadresser som du anger. Jämför resultaten för att verifiera att sårbarheter har åtgärdats.

- [Så här exporterar du en sårbarhetsbedömningsrapport i Azure Security Center](/azure/sql-database/sql-vulnerability-assessment#implementing-vulnerability-assessment)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Använd en riskklassificeringsprocess för att prioritera åtgärder för identifierade säkerhetsrisker

**Vägledning:** Använd standardriskklassificeringar (säkerhetspoäng) som tillhandahålls av Azure Security Center.

- [Förstå Azure Security Center säkerhetspoäng](/azure/security-center/security-center-secure-score)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.5](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-5.md)]

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Benchmark för Azure-säkerhet: Inventering och tillgångshantering.](../../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Använd lösningen för automatisk tillgångsidentifiering

**Vägledning:** Använd Azure Resource Graph för att fråga efter och identifiera alla resurser (Azure SQL Server-instanser) i dina prenumerationer. Se till att du har rätt (läsbehörighet) i din klientorganisation och att du kan räkna upp alla Azure-prenumerationer och resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan Resource Graph via en Resource Graph rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser i framtiden.

- [Så här skapar du frågor med Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription)

- [Förstå Azure RBAC](../../role-based-access-control/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="62-maintain-asset-metadata"></a>6.2: Underhålla tillgångsmetadata

**Vägledning:** Tillämpa taggar på Azure-resurser som ger metadata för att logiskt organisera dem i en taxonomi.

- [Skapa och använda taggar](/azure/azure-resource-manager/resource-group-using-tags)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ta bort obehöriga Azure-resurser

**Vägledning:** Använd taggar, hanteringsgrupper och separata prenumerationer när det är lämpligt för att organisera och spåra tillgångar. Stämma av inventeringen regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

- [Så här skapar du ytterligare Azure-prenumerationer](/azure/billing/billing-create-subscription)

- [Så här skapar du Hanteringsgrupper](/azure/governance/management-groups/create)

- [Skapa och använda taggar](/azure/azure-resource-manager/resource-group-using-tags)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Övervaka för ej godkända Azure-resurser

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationerna med hjälp av följande inbyggda principdefinitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Använd Azure Resource Graph för att fråga efter/identifiera resurser i dina prenumerationer. Se till att alla Azure-resurser som finns i miljön godkänns.

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="69-use-only-approved-azure-services"></a>6.9: Använd endast godkända Azure-tjänster

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationerna med hjälp av följande inbyggda principdefinitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

Använd Azure Resource Graph för att fråga efter/identifiera resurser i dina prenumerationer. Se till att alla Azure-resurser som finns i miljön godkänns.

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en viss resurstyp med Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning:** Använd villkorsstyrd åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "Blockera åtkomst" för appen "Microsoft Azure management".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security Benchmark: Secure Configuration](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning:** Använd Azure Policy eller Azure Security Center rekommendationer för Azure SQL-servrar/databaser för att underhålla säkerhetskonfigurationer för alla Azure-resurser.

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Underhålla säkra Azure-resurskonfigurationer

**Vägledning:** Använd Azure Policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar för alla dina Azure-resurser.

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Förstå Azure Policy effekter](../../governance/policy/concepts/effects.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Lagra konfiguration av Azure-resurser på ett säkert sätt

**Vägledning:** Om du använder anpassade Azure Policy bör du använda Azure DevOps eller Azure Repos för att lagra och hantera din kod på ett säkert sätt.

- [Lagra kod i Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Dokumentation om Azure Repos](/azure/devops/repos/)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuera konfigurationshanteringsverktyg för Azure-resurser

**Vägledning:** Använd Azure Policy alias i namnområdet "Microsoft.SQL" för att skapa anpassade principer för att varna, granska och framtvinga systemkonfigurationer. Utveckla dessutom en process och pipeline för hantering av principundantag.

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementera automatisk konfigurationsövervakning för Azure-resurser

**Vägledning:** Använd Azure Security Center för att utföra baslinjesökningar för dina Azure SQL och databaser.

- [Så här åtgärdar du rekommendationer i Azure Security Center](/azure/security-center/security-center-sql-service-recommendations)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning:** Använd Azure Key Vault för att lagra krypteringsnycklar för Azure SQL Database transparent datakryptering (TDE).

- [Så här skyddar du känsliga data som lagras Azure SQL Server och lagrar krypteringsnycklarna i Azure Key Vault](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Hantera identiteter på ett säkert och automatiskt sätt

**Vägledning:** Använda hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet Azure Active Directory (Azure AD). Med hanterade identiteter kan du autentisera till alla tjänster som stöder Azure AD-autentisering, Azure Key Vault, utan några autentiseringsuppgifter i koden.

- [Självstudie: Använda en systemtilldelad hanterad identitet för en virtuell Windows-dator för åtkomst till Azure SQL](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)

- [Så här konfigurerar du hanterade identiteter](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminera oavsiktlig exponering av autentiseringsuppgifter

**Vägledning:** Implementera skanner för autentiseringsuppgifter för att identifiera autentiseringsuppgifter i koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault. 

- [Konfigurera skanner för autentiseringsuppgifter](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security Benchmark: Malware Defense.](../../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Skanna filer i förväg som ska laddas upp till Icke-beräkningsbaserade Azure-resurser

**Vägledning:** Microsofts program mot skadlig kod är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure App Service), men den körs inte på kundinnehåll.

Skanna innehåll som överförs i förväg till Azure-resurser som inte är beräkningsbaserade, till exempel App Service, Data Lake Storage, Blob Storage, Azure SQL Server osv. Microsoft kan inte komma åt dina data i dessa instanser.

- [Förstå Microsoft Antimalware för Azure Cloud Services och Virtual Machines](../../security/fundamentals/antimalware.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Prestandatest för Azure-säkerhet: Dataåterställning.](../../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Säkerställ regelbundna automatiserade säkerhetskopieringar

**Vägledning:** För att skydda ditt företag mot dataförlust skapar Azure SQL Database automatiskt fullständiga databassäkerhetskopior varje vecka, differentiella databassäkerhetskopior var 12:e timme och säkerhetskopior av transaktionsloggar var 5:e till var 10:e minut. Säkerhetskopiorna lagras i RA-GRS-lagring i minst 7 dagar för alla tjänstnivåer. Alla tjänstnivåer utom de Basic Support kvarhållningsperioden för säkerhetskopior som kan konfigureras för återställning till en viss tidpunkt, upp till 35 dagar.

För att uppfylla olika efterlevnadskrav kan du välja olika kvarhållningsperioder för veckovisa, månatliga och/eller årliga säkerhetskopior. Lagringsförbrukningen beror på den valda säkerhetskopieringsfrekvensen och kvarhållningsperioden.

- [Förstå säkerhetskopiering och affärskontinu hos Azure SQL Server](/azure/sql-database/sql-database-business-continuity)

**Ansvar**: Delad

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utför fullständiga systemsäkerhetskopior och säkerhetskopiera kund hanterade nycklar

**Vägledning:** Azure SQL Database skapar automatiskt databassäkerhetskopior som sparas mellan 7 och 35 dagar och använder Azure READ-Access Geo Redundant Storage (RA-GRS) för att säkerställa att de bevaras även om datacentret inte är tillgängligt. Dessa säkerhetskopior skapas automatiskt. Om det behövs aktiverar du långsiktiga geo-redundanta säkerhetskopieringar för dina Azure SQL Database.

Om du använder kund hanterade nycklar transparent datakryptering måste du kontrollera att dina nycklar säkerhetskopieras.

- [Förstå säkerhetskopieringar i Azure SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database)

- [Säkerhetskopiera nyckelvalvsnycklar i Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.2](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Verifiera alla säkerhetskopior, inklusive kund hanterade nycklar

**Vägledning:** Säkerställ möjligheten att regelbundet utföra dataåterställning av innehåll inom Azure Backup. Om det behövs testar du återställning av innehåll till ett isolerat VLAN. Testa återställning av säkerhetskopierade kund hanterade nycklar.

- [Återställa nyckelvalvsnycklar i Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Så här återställer Azure SQL Database säkerhetskopior med hjälp av återställning till tidpunkt](/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Skydda säkerhetskopior och kund hanterade nycklar

**Vägledning:** Aktivera mjuk borttagning i Azure Key Vault att skydda nycklar mot oavsiktlig eller skadlig borttagning.

- [Så här aktiverar du mjuk borttagning i Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Skapa en incidentsvarsguide

**Vägledning:** Se till att det finns skriftliga planer för incidenthantering som definierar roller för personal samt faser för incidenthantering/incidenthantering.

- [Så här konfigurerar du arbetsflödesautomation inom Azure Security Center](../../security-center/security-center-planning-and-operations-guide.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Skapa en procedur för bedömning och prioritering av incidenter

**Vägledning:** Security Center tilldelar aviseringar en allvarlighetsgrad för att hjälpa dig att prioritera i vilken ordning du deltar i varje avisering, så att när en resurs komprometteras kan du komma åt den direkt. Allvarlighetsgraden baseras på hur säker Security Center är i hitta eller analys som används för att utfärda aviseringen samt konfidensnivån för att det fanns skadlig avsikt bakom aktiviteten som ledde till aviseringen.

- [Säkerhetsaviseringar i Azure Security Center](../../security-center/security-center-alerts-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="103-test-security-response-procedures"></a>10.3: Testa procedurer för säkerhetssvar

**Vägledning:** Genomför övningar för att regelbundet testa systemets funktioner för incidenter. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [Du kan referera till NIST:s publikation: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Guide till test-, tränings- och övningsprogram för IT-planer och IT-funktioner)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Ange kontaktuppgifter för säkerhetsincidenter och konfigurera aviseringsmeddelanden för säkerhetsincidenter

**Vägledning:** Kontaktinformation om säkerhetsincident används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en obehörig part.

- [Ställa in Azure Security Center säkerhetskontakt](../../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Införliva säkerhetsaviseringar i systemet för incidenter

**Vägledning:** Exportera dina Azure Security Center och rekommendationer med hjälp av funktionen Kontinuerlig export. Med löpande export kan du exportera aviseringar och rekommendationer manuellt eller kontinuerligt. Du kan använda anslutningsappen Azure Security Center data för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du löpande export](../../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisera svaret på säkerhetsaviseringar

**Vägledning:** Använd funktionen Arbetsflödesautomation i Azure Security Center att automatiskt utlösa svar via "Logic Apps" för säkerhetsaviseringar och rekommendationer.

- [Så här konfigurerar du arbetsflödesautomation och Logic Apps](../../security-center/workflow-automation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [Azure Security Benchmark: Penetration Tests (Intrångstester) och Red Team Exercises (Red Team-övningar).](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Genomför regelbundna intrångstester av dina Azure-resurser och se till att alla viktiga säkerhetsresultat åtgärdas

**Vägledning:** Följ Microsoft Cloud Penetration Testing Rules of Engagement för att säkerställa att dina intrångstester inte bryter mot Microsofts principer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft. 

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
