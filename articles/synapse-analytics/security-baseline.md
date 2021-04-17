---
title: Azure-säkerhetsbaslinje för Synapse Analytics
description: Säkerhetsbaslinjen Synapse Analytics innehåller procedurvägledning och resurser för att implementera säkerhetsrekommendationerna som anges i Azure Security Benchmark.
author: msmbaldwin
ms.service: synapse-analytics
ms.subservice: security
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1a426a86ddb25733b8425c5887d2c0522f915e2d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587722"
---
# <a name="azure-security-baseline-for-synapse-analytics"></a>Azure-säkerhetsbaslinje för Synapse Analytics

Den här säkerhetsbaslinjen tillämpar riktlinjer [från Azure Security Benchmark version 1.0](../security/benchmarks/overview-v1.md) Synapse Analytics. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure.
Innehållet grupperas efter de säkerhetskontroller **som definierats** av Azure Security Benchmark och tillhörande vägledning som gäller för Synapse Analytics. **Kontroller** som inte är Synapse Analytics har undantagits.

 
Information om hur Synapse Analytics mappning helt till Azure Security Benchmark finns i den fullständiga Synapse Analytics [säkerhetsbaslinjemappningsfilen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Skydda Azure-resurser i virtuella nätverk

**Vägledning:** Skydda din Azure SQL Server till ett virtuellt nätverk via Private Link. Azure Private Link kan du komma åt Azure PaaS-tjänster via en privat slutpunkt i ditt virtuella nätverk. Trafiken mellan ditt virtuella nätverk och tjänsten färdas via Microsofts stamnätverk.

När du ansluter till din Synapse SQL-pool kan du också begränsa omfånget för den utgående anslutningen till SQL-databasen med hjälp av en nätverkssäkerhetsgrupp. Inaktivera all Azure-tjänsttrafik till SQL-databasen via den offentliga slutpunkten genom att ange Tillåt Azure-tjänster till AV. Se till att inga offentliga IP-adresser tillåts i brandväggsreglerna.

- [Förstå Azure Private Link](../private-link/private-link-overview.md)

- [Förstå Private Link för Azure Synapse SQL](../azure-sql/database/private-endpoint-overview.md)

- [Så här skapar du en Virtual Network](../virtual-network/quick-create-portal.md)

- [Så här skapar du en NSG med en säkerhetskonfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Övervaka och logga konfiguration och trafik för virtuella nätverk, undernät och nätverksgränssnitt

**Vägledning:** När du ansluter till din dedikerade SQL-pool och du har aktiverat NSG-flödesloggar (nätverkssäkerhetsgrupp) skickas loggar till ett Azure Storage-konto för trafikgranskning.

Du kan också skicka NSG-flödesloggar till en Log Analytics-arbetsyta och använda Trafikanalys för att ge insikter om trafikflödet i ditt Azure-moln. Vissa fördelar med Trafikanalys är möjligheten att visualisera nätverksaktivitet och identifiera hotpunkter, identifiera säkerhetshot, förstå trafikflödesmönster och hitta felkonfigurationer i nätverk.

- [Så här aktiverar du NSG-flödesloggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Förstå nätverkssäkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Aktivera och använda Trafikanalys](../network-watcher/traffic-analytics.md)

- [Förstå nätverkssäkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

**Vägledning:** Använda Advanced Threat Protection (ATP) för Azure Synapse SQL. ATP identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser och kan utlösa olika aviseringar, till exempel "potentiell SQL-ktion" och "åtkomst från ovanlig plats". ATP är en del av ADS-erbjudandet (Advanced Data Security) och kan nås och hanteras via den centrala SQL ADS-portalen.

Aktivera DDoS Protection Standard på de virtuella nätverk som är associerade med Azure Synapse SQL för skydd mot distribuerade DoS-attacker. Använd Azure Security Center Integrated Threat Intelligence för att neka kommunikation med kända skadliga eller oanvända Internet-IP-adresser.

- [Förstå ATP för Azure Synapse SQL](../azure-sql/database/threat-detection-overview.md)

- [Så här aktiverar du Advanced Data Security för Azure SQL Database](../azure-sql/database/azure-defender-for-sql.md)

- [ÖVERSIKT ÖVER ADS](../azure-sql/database/azure-defender-for-sql.md)

- [Så här konfigurerar du DDoS-skydd](../ddos-protection/manage-ddos-protection.md)

- [Förstå Azure Security Center Integrated Threat Intelligence](../security-center/azure-defender.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="15-record-network-packets"></a>1.5: Registrera nätverkspaket

**Vägledning:** När du ansluter till din dedikerade SQL-pool och du har aktiverat flödesloggar för nätverkssäkerhetsgrupp (NSG) skickar du loggar till ett Azure Storage-konto för trafikgranskning. Du kan också skicka flödesloggar till en Log Analytics-arbetsyta eller strömma dem till Event Hubs.  Om det krävs för att undersöka avvikande aktivitet aktiverar du Network Watcher infångade paket.

- [Så här aktiverar du NSG-flödesloggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar du Network Watcher](../network-watcher/network-watcher-create.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuera nätverksbaserade system för intrångsidentifiering/intrångsskydd (IDS/IPS)

**Vägledning:** Använd Advanced Threat Protection (ATP) för Azure Synapse SQL. ATP identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser och kan utlösa olika aviseringar, till exempel "potentiell SQL- injection" och "åtkomst från ovanlig plats". ATP är en del av ADS-erbjudandet (Advanced Data Security) och kan nås och hanteras via den centrala SQL ADS-portalen. ATP integrerar även aviseringar med Azure Security Center.

- [Förstå ATP för Azure Synapse SQL](../azure-sql/database/threat-detection-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexitet och administrativa kostnader för nätverkssäkerhetsregler

**Vägledning:** Använd tjänsttaggar för virtuella nätverk för att definiera nätverksåtkomstkontroller för nätverkssäkerhetsgrupper eller Azure Firewall. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttaggnamnet (t.ex. ApiManagement) i lämpligt käll- eller målfält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras.

När du använder en tjänstslutpunkt för din dedikerade SQL-pool krävs utgående till Azure SQL-databasens offentliga IP-adresser: Nätverkssäkerhetsgrupper (NSG:er) måste öppnas för Azure SQL Database IP-adresser för att tillåta anslutning. Du kan göra detta med hjälp av NSG-tjänsttaggar för Azure SQL Database.

- [Förstå tjänsttaggar med tjänstslutpunkter för Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/database/vnet-service-endpoint-rule-overview#limitations)

- [Förstå och använda tjänsttaggar](../virtual-network/service-tags-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Underhålla standardsäkerhetskonfigurationer för nätverksenheter

**Vägledning:** Definiera och implementera nätverkssäkerhetskonfigurationer för resurser relaterade till din dedikerade SQL-pool med Azure Policy. Du kan använda namnområdet "Microsoft.Sql" för att definiera anpassade principdefinitioner eller använda någon av de inbyggda principdefinitionerna som utformats för Azure SQL-databas-/servernätverksskydd. Ett exempel på en tillämplig inbyggd nätverkssäkerhetsprincip för Azure SQL Database server är: "SQL Server ska använda en tjänstslutpunkt för virtuellt nätverk".

Använd Azure Blueprints för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljöartefakter, till exempel Azure Resource Management-mallar, rollbaserad åtkomstkontroll (Azure RBAC) och principer i en enda skissdefinition. Tillämpa enkelt skissen på nya prenumerationer och miljöer och finjustera kontroll och hantering via versionshantering.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du en Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentera trafikkonfigurationsregler

**Vägledning:** Använd taggar för nätverkssäkerhetsgrupper (NSG) och andra resurser som rör nätverkssäkerhet och trafikflöde. För enskilda NSG-regler använder du fältet "Beskrivning" för att ange affärs behov och/eller varaktighet (osv.) för regler som tillåter trafik till/från ett nätverk.

Använd någon av de inbyggda Azure Policy-definitionerna som rör taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och för att meddela dig om befintliga otaggade resurser.

Du kan använda Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

**Vägledning:** Använd Azure-aktivitetsloggen för att övervaka nätverksresurskonfigurationer och identifiera ändringar för nätverksresurser som är relaterade till din dedikerade SQL-pool. Skapa aviseringar i Azure Monitor som utlöses när ändringar av viktiga nätverksresurser sker.

- [Så här visar och hämtar du Händelser i Azure-aktivitetsloggen](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i Azure Security Benchmark: Logging and Monitoring ( [Prestandatest för Azure-säkerhet: Loggning och övervakning).](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera central hantering av säkerhetsloggar

**Vägledning:** En granskningsprincip kan definieras för en specifik databas eller som en standardserverprincip i Azure (som är värd för Azure Synapse). En serverprincip gäller för alla befintliga och nyligen skapade databaser på servern.

Om servergranskning är aktiverat gäller det alltid för databasen. Databasen granskas, oavsett inställningarna för databasgranskning.

När du aktiverar granskning kan du skriva dem till en granskningslogg i Azure Storage-konto, Log Analytics-arbetsyta eller Event Hubs.

Du kan också aktivera och ta med data till Azure Sentinel eller en SIEM från tredje part.

- [Konfigurera granskning för dina Azure SQL resurser](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägledning:** Aktivera granskning på Azure SQL-servernivå för din dedikerade SQL-pool och välj en lagringsplats för granskningsloggarna (Azure Storage, Log Analytics eller Event Hubs).

Granskning kan aktiveras både på databas- eller servernivå och rekommenderas att endast aktiveras på servernivå, såvida du inte behöver konfigurera en separat data mottagare eller kvarhållning för en specifik databas.

- [Så här aktiverar du granskning för Azure SQL Database](../azure-sql/database/auditing-overview.md)

- [Så här aktiverar du granskning för servern](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#setup-auditing)

- [Skillnader i granskningsprinciper på servernivå jämfört med databasnivå](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera kvarhållning av säkerhetslogglagring

**Vägledning:** När du lagrar loggar relaterade till din dedikerade SQL-pool i ett lagringskonto, en Log Analytics-arbetsyta eller händelsehubbar ska du ange kvarhållningsperiod för loggar enligt organisationens efterlevnadsregler.

- [Hantera Azure Blob Storage-livscykeln](../storage/blobs/storage-lifecycle-management-concepts.md)

- [Så här anger du parametrar för loggbevarande på en Log Analytics-arbetsyta](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Samla in direktuppspelningshändelser i Event Hubs](../event-hubs/event-hubs-capture-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-5.md)]

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning:** Analysera och övervaka loggar för avvikande beteenden och granska resultaten regelbundet. Använd Advanced Threat Protection för Azure SQL Database tillsammans med Azure Security Center för att varna om ovanlig aktivitet som rör din SQL-databas. Du kan också konfigurera aviseringar baserat på måttvärden eller Azure-aktivitetsloggposter som är relaterade till din SQL-databas.

Du kan också aktivera och ta med data för att Azure Sentinel eller en SIEM från tredje part.

- [Förstå Advanced Threat Protection och aviseringar för Azure SQL Database](../azure-sql/database/threat-detection-overview.md)

- [Så här aktiverar du Advanced Data Security för Azure SQL Database](../azure-sql/database/azure-defender-for-sql.md)

- [Så här konfigurerar du anpassade aviseringar för Azure SQL Database](../azure-sql/database/alerts-insights-configure-portal.md)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivera aviseringar för avvikande aktiviteter

**Vägledning:** Använd Advanced Threat Protection (ATP) för Azure SQL Database tillsammans med Azure Security Center för att övervaka och varna vid avvikande aktivitet. ATP ingår i ADS-erbjudandet (Advanced Data Security) och kan nås och hanteras via centrala SQL ADS i portalen. ADS innehåller funktioner för att identifiera och klassificera känsliga data, visa och åtgärda potentiella säkerhetsrisker i databasen samt identifiera avvikande aktiviteter som kan tyda på ett hot mot din databas.

Du kan också aktivera och ta med data till Azure Sentinel.

- [Förstå Advanced Threat Protection och aviseringar för Azure SQL Database](../azure-sql/database/threat-detection-overview.md)

- [Så här aktiverar du Advanced Data Security för Azure SQL Database](../azure-sql/database/azure-defender-for-sql.md)

- [Hantera aviseringar i Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.7](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-7.md)]

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Upprätthålla en förteckning över administrativa konton

**Vägledning:** Användare autentiseras med antingen Azure Active Directory (Azure AD) eller SQL-autentisering.

När du först distribuerar Azure SQL anger du en administratörsinloggning och ett associerat lösenord för inloggningen. Det här administrativa kontot kallas serveradministratör. Du kan identifiera administratörskonton för en databas genom att öppna Azure Portal och navigera till egenskapsfliken för din server eller hanterade instans. Du kan också konfigurera ett Azure AD-administratörskonto med fullständig administratörsbehörighet. Detta krävs om du vill aktivera Azure AD-autentisering.

För hanteringsåtgärder använder du de inbyggda Azure-rollerna som måste tilldelas explicit. Använd Azure AD PowerShell-modulen för att köra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

- [Autentisering för SQL Database](https://docs.microsoft.com/azure/azure-sql/database/security-overview#authentication)

- [Skapa konton för icke-administrativa användare](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-accounts-for-non-administrator-users)

- [Använda ett Azure AD-konto för autentisering](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-additional-logins-and-users-having-administrative-permissions)

- [Så här får du en katalogroll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Hämta medlemmar i en katalogroll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Hantera befintliga inloggningar och administratörskonton i Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

- [Inbyggda roller i Azure](../role-based-access-control/built-in-roles.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändra standardlösenord om tillämpligt

**Vägledning:** Azure Active Directory (Azure AD) har inte begreppet standardlösenord. När du etablerar en dedikerad SQL-pool rekommenderar vi att du väljer att integrera autentisering med Azure AD. Med den här autentiseringsmetoden skickar användaren ett användarkontonamn och begär att tjänsten använder autentiseringsinformationen som lagras i Azure AD.

- [Så här konfigurerar och hanterar du Azure AD-autentisering med Azure SQL](../azure-sql/database/authentication-aad-configure.md)

- [Förstå autentisering i Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Använd dedikerade administrativa konton

**Vägledning:** Skapa principer och procedurer kring användningen av dedikerade administrativa konton. Använd Azure Security Center identitets- och åtkomsthantering för att övervaka antalet administrativa konton som loggar in via Azure Active Directory (Azure AD).

Om du vill identifiera administratörskonton för en databas öppnar du Azure Portal och går till fliken Egenskaper för servern eller den hanterade instansen.

- [Förstå Azure Security Center identitet och åtkomst](../security-center/security-center-identity-access.md)

- [Hantera befintliga inloggningar och administratörskonton i Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Använd Azure Active Directory enkel inloggning (SSO)

**Vägledning:** Använd en Azure-appregistrering (tjänstens huvudnamn) för att hämta en token som kan användas för att interagera med ditt informationslager på kontrollplanet (Azure Portal) via API-anrop.

- [Så här anropar du Azure REST API:er](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Registrera klientprogrammet (tjänstens huvudnamn) med Azure Active Directory (Azure AD)](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Azure Synapse sql REST API information](sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använd multifaktorautentisering för Azure Active Directory åtkomstbaserad åtkomst

**Vägledning:** Aktivera Azure Active Directory (Azure AD) multifaktorautentisering och följ rekommendationer Azure Security Center identitets- och åtkomsthantering.

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst inom Azure Security Center](../security-center/security-center-identity-access.md)

- [Förstå multifaktorautentisering i Azure SQL](../azure-sql/database/authentication-mfa-ssms-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Använd säkra, Azure-hanterade arbetsstationer för administrativa uppgifter

**Vägledning:** Använd en PAW (Privileged Access Workstation) med multifaktorautentisering som konfigurerats för att logga in på och konfigurera Azure-resurser.

- [Läs mer om arbetsstationer med privilegierad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logga och varna om misstänkta aktiviteter från administrativa konton

**Vägledning:** Använd Azure Active Directory (Azure AD) för generering av loggar och aviseringar när misstänkt eller osäker aktivitet inträffar i miljön.

Använd Advanced Threat Protection för Azure SQL Database tillsammans med Azure Security Center för att identifiera och varna vid avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

SQL Server granskning kan du skapa servergranskningar som kan innehålla servergranskningsspecifikationer för händelser på servernivå och databasgranskningsspecifikationer för händelser på databasnivå. Granskade händelser kan skrivas till händelseloggarna eller till granskningsfiler.

- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](../active-directory/identity-protection/overview-identity-protection.md)

- [Övervaka användarnas identitets- och åtkomstaktivitet i Azure Security Center](../security-center/security-center-identity-access.md)

- [Granska Advanced Threat Protection och potentiella aviseringar](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview#alerts)

- [Förstå inloggningar och användarkonton i Azure SQL](../azure-sql/database/logins-create-manage.md)

- [Förstå SQL Server granskning](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Hantera Azure-resurser från endast godkända platser

**Vägledning:** Använd villkorsstyrd åtkomst med namngivna platser för att tillåta portal- och Azure Resource Management-åtkomst från endast specifika logiska grupper av IP-adressintervall eller länder/regioner.

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning:** Skapa en Azure Active Directory (Azure AD)-administratör för Azure SQL Database i din dedikerade SQL-pool.

- [Så här konfigurerar och hanterar du Azure AD-autentisering med Azure SQL](../azure-sql/database/authentication-aad-configure.md)

- [Skapa och konfigurera en Azure AD-instans](../active-directory-domain-services/tutorial-create-instance.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 3.9](../../includes/policy/standards/asb/rp-controls/microsoft.sql-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Granska och stämma av användaråtkomst regelbundet

**Vägledning:** Azure Active Directory (Azure AD) innehåller loggar som hjälper dig att identifiera inaktuella konton. Använd dessutom Azure AD-åtkomstgranskningar för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Användarnas åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

När du använder SQL-autentisering skapar du inneslutna databasanvändare i databasen. Se till att du placerar en eller flera databasanvändare i en anpassad databasroll med specifika behörigheter som är lämpliga för den gruppen av användare.

- [Så här använder du åtkomstgranskningar](../active-directory/governance/access-reviews-overview.md)

- [Förstå inloggningar och användarkonton i Azure SQL](../azure-sql/database/logins-create-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning:** Konfigurera Azure Active Directory-autentisering (Azure AD) med Azure SQL och aktivera diagnostikinställningar för Azure AD-användarkonton, skicka granskningsloggar och inloggningsloggar till en Log Analytics-arbetsyta. Konfigurera önskade aviseringar i Log Analytics.

När du använder SQL-autentisering skapar du användare av inneslutna databaser i databasen. Se till att du placerar en eller flera databasanvändare i en anpassad databasroll med specifika behörigheter som är lämpliga för den gruppen av användare.

- [Så här använder du åtkomstgranskningar](../active-directory/governance/access-reviews-overview.md)

- [Så här konfigurerar och hanterar du Azure AD-autentisering med Azure SQL Database](../azure-sql/database/authentication-aad-configure.md)

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Förstå inloggningar och användarkonton i Azure SQL](../azure-sql/database/logins-create-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Avisering om kontots beteendeavvikelse vid inloggning

**Vägledning:** Använd Azure Active Directory (Azure AD) Identity Protection och riskidentifieringsfunktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder relaterade till användaridentiteter. Dessutom kan du ta med och mata in data Azure Sentinel ytterligare undersökning.

När du använder SQL-autentisering skapar du användare av inneslutna databaser i databasen. Se till att du placerar en eller flera databasanvändare i en anpassad databasroll med specifika behörigheter som är lämpliga för den gruppen av användare.

- [Så här visar du azure AD-risk inloggningar](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du riskprinciper för Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Så här Azure Sentinel](../sentinel/connect-data-sources.md)

- [Förstå inloggningar och användarkonton i Azure SQL](../azure-sql/database/logins-create-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ge Microsoft åtkomst till relevanta kunddata under supportscenarier

**Vägledning:** I supportscenarier där Microsoft behöver komma åt data som rör Azure SQL Database i din dedikerade SQL-pool tillhandahåller Azure Customer Lockbox ett gränssnitt där du kan granska och godkänna eller avvisa begäranden om dataåtkomst.

- [Förstå Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Upprätthålla en förteckning över känslig information

**Vägledning:** Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information.

Klassificering av &amp; dataidentifiering är inbyggt i Azure Synapse SQL. Den här funktionen ger avancerade funktioner för identifiering, klassificering, märkning och rapportering av känsliga data i databasen.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

- [Förstå klassificering av &amp; dataidentifiering](../azure-sql/database/data-discovery-and-classification-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-1.md)]

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolera system som lagrar eller bearbetar känslig information

**Vägledning:** Implementera separata prenumerationer och/eller hanteringsgrupper för utveckling, testning och produktion. Resurserna ska avgränsas med virtuellt nätverk/undernät, taggas på rätt sätt och skyddas i en nätverkssäkerhetsgrupp eller Azure Firewall. Resurser som lagrar eller bearbetar känsliga data bör isoleras. Använd Private Link; distribuera din Azure SQL Server i ett virtuellt nätverk och anslut säkert med Private Link.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

- [Konfigurera en Private Link för Azure SQL Database](../azure-sql/database/private-endpoint-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Övervaka och blockera obehörig överföring av känslig information

**Vägledning:** För alla Azure SQL Database i din dedikerade SQL-pool som lagrar eller bearbetar känslig information markerar du databasen och relaterade resurser som känsliga med hjälp av taggar. Konfigurera Private Link tillsammans med tjänsttaggar för nätverkssäkerhetsgrupp (NSG) på dina Azure SQL Database instanser för att förhindra exfiltrering av känslig information.

Dessutom identifierar Advanced Threat Protection för Azure SQL Database, Azure SQL Managed Instance och Azure Synapse avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt och tar lång tid att skydda mot förlust och exponering av kunddata. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och upprätthåller en uppsättning robusta dataskyddskontroller och -funktioner.

- [Så här konfigurerar Private Link och NSG:er för att förhindra data exfiltrering på dina Azure SQL Database instanser](../azure-sql/database/private-endpoint-overview.md)

- [Förstå Advanced Threat Protection för Azure SQL Database](../azure-sql/database/threat-detection-overview.md)

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Använd ett aktivt identifieringsverktyg för att identifiera känsliga data

**Vägledning:** Använd Azure Synapse SQL Data &amp; Discovery-klassificering. Data Discovery Classification innehåller avancerade funktioner som är inbyggda Azure SQL Database för identifiering, klassificering och etikettering som skyddar &amp; känsliga data i dina &amp; databaser.

Data Discovery &amp; Classification ingår i Advanced Data Security, som är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Klassificering av &amp; dataidentifiering kan nås och hanteras via den centrala SQL ADS-portalen.

Dessutom kan du konfigurera en princip för dynamisk datamaskering (DDM) i Azure Portal. DDM-rekommendationsmotorn flaggar vissa fält från databasen som potentiellt känsliga fält som kan vara lämpliga för maskering.

- [Använda dataidentifiering och -klassificering för Azure SQL Server](../azure-sql/database/data-discovery-and-classification-overview.md)

- [Förstå dynamisk datamaskering för Azure Synapse SQL](../azure-sql/database/dynamic-data-masking-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-5.md)]

### <a name="46-use-azure-rbac-access-control-to-control-access-to-resources"></a>4.6: Använd Azure RBAC-åtkomstkontroll för att styra åtkomsten till resurser 

**Vägledning:** Använd rollbaserad åtkomstkontroll i Azure (Azure RBAC) för att hantera åtkomst Azure SQL databaser i din dedikerade SQL-pool.

Auktoriseringen styrs av ditt användarkontos databasrollmedlemskap och behörigheter på objektnivå. Ett bra tips är att du ska ge användare så få behörigheter som möjligt.

- [Integrera Azure SQL Server med Azure Active Directory (Azure AD) för autentisering](../azure-sql/database/authentication-aad-overview.md)

- [Så här styr du åtkomst i Azure SQL Server](../azure-sql/database/logins-create-manage.md)

- [Förstå auktorisering och autentisering i Azure SQL](../azure-sql/database/logins-create-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Kryptera känslig information i vila

**Vägledning:** Transparent datakryptering (TDE) hjälper till att skydda Azure Synapse SQL mot hot om skadlig offlineaktivitet genom att kryptera vilodata. TDE utför realtidskryptering och realtidsdekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler i vila, utan att några ändringar krävs i programmet. I Azure är standardinställningen för TDE att DEK skyddas av ett inbyggt servercertifikat. Du kan också använda kund-hanterad TDE, även kallat BYOK-stöd (Bring Your Own Key) för TDE. I det här scenariot är TDE-skyddet som krypterar DEK en kund hanterad asymmetrisk nyckel som lagras i en kundägd och hanterad Azure Key Vault (Azures molnbaserade externa nyckelhanteringssystem) och lämnar aldrig nyckelvalvet.

- [Förstå tjänst-hanterad transparent datakryptering](../azure-sql/database/transparent-data-encryption-tde-overview.md)

- [Förstå kund hanterad transparent datakryptering](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview#customer-managed-transparent-data-encryption---bring-your-own-key)

- [Så här aktiverar du TDE med din egen nyckel](../azure-sql/database/transparent-data-encryption-byok-configure.md)

**Ansvar**: Delad

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisering om ändringar av viktiga Azure-resurser

**Vägledning:** Använd Azure Monitor med Azure-aktivitetsloggen för att skapa aviseringar för när ändringar sker i produktionsinstanser av Synapse SQL-pooler och andra kritiska eller relaterade resurser.

Dessutom kan du ställa in aviseringar för databaser i din SQL Synapse-pool med hjälp av Azure Portal. Aviseringar kan skicka dig ett e-postmeddelande eller anropa en web hook när ett mått (till exempel databasstorlek eller CPU-användning) når tröskelvärdet.

- [Så här skapar du aviseringar för Azure-aktivitetslogghändelser](../azure-monitor/alerts/alerts-activity-log.md)

- [Skapa aviseringar för Azure SQL Synapse](../azure-sql/database/alerts-insights-configure-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Köra automatiserade sårbarhetsgenomsökningsverktyg

**Vägledning:** Aktivera Advanced Data Security följa rekommendationer från Azure Security Center att utföra sårbarhetsbedömningar på Azure SQL databaser.

- [Så här kör du sårbarhetsbedömningar på Azure SQL databaser](../azure-sql/database/sql-vulnerability-assessment.md)

- [Så här aktiverar du Advanced Data Security](../azure-sql/database/azure-defender-for-sql.md)

- [Så här implementerar du Azure Security Center rekommendationer för sårbarhetsbedömning](../security-center/deploy-vulnerability-assessment-vm.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-1.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Jämför tillbaka-till-tillbaka-sårbarhetsgenomsökningar

**Vägledning:** Sårbarhetsbedömning är en genomsökningstjänst som är inbyggd i Azure Synapse SQL. Tjänsten använder en kunskapsbas med regler som flaggar säkerhetsproblem. Den visar avvikelser från bästa praxis, till exempel felaktiga konfigurationer, överdrivna behörigheter och oskyddade känsliga data.  Sårbarhetsbedömning kan nås och hanteras via den centrala SQL Advanced Data Security-portalen (ADS).

- [Hantera och exportera genomsökningar av sårbarhetsbedömningar i SQL ADS-portalen](../azure-sql/database/sql-vulnerability-assessment.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Använd en riskklassificeringsprocess för att prioritera åtgärder för identifierade säkerhetsrisker

**Vägledning:** Använd standardriskklassificeringar (säkerhetspoäng) som tillhandahålls av Azure Security Center.

Klassificering av &amp; dataidentifiering är inbyggt i Azure Synapse SQL. Den här funktionen ger avancerade funktioner för identifiering, klassificering, märkning och rapportering av känsliga data i databasen.

- [Förstå Azure Security Center säkerhetspoäng](../security-center/secure-score-security-controls.md)

- [Förstå klassificering av &amp; dataidentifiering](../azure-sql/database/data-discovery-and-classification-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-5.md)]

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Benchmark för Azure-säkerhet: Inventering och tillgångshantering.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Använd automatisk tillgångsidentifiering

**Vägledning:** Använd Azure Resource Graph för att fråga efter och identifiera alla resurser som är relaterade till din dedikerade SQL-pool i dina prenumerationer. Se till att du har rätt (läsbehörighet) i din klientorganisation och att du kan räkna upp alla Azure-prenumerationer och resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via Azure Resource Graph rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser i framtiden.

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="62-maintain-asset-metadata"></a>6.2: Underhålla tillgångsmetadata

**Vägledning:** Tillämpa taggar på Azure-resurser som ger metadata för att logiskt organisera dem i en taxonomi.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ta bort obehöriga Azure-resurser

**Vägledning:** Använd taggning, hanteringsgrupper och separata prenumerationer när det är lämpligt för att organisera och spåra tillgångar. Stämma av inventeringen regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definiera och underhålla inventering av godkända Azure-resurser

**Vägledning:** Definiera en lista över godkända Azure-resurser relaterade till din dedikerade SQL-pool.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Övervaka för ej godkända Azure-resurser

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Använd Azure Resource Graph för att fråga efter/identifiera resurser i dina prenumerationer. Se till att alla Azure-resurser som finns i miljön godkänns.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="69-use-only-approved-azure-services"></a>6.9: Använd endast godkända Azure-tjänster

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationerna med hjälp av följande inbyggda principdefinitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

Använd Azure Resource Graph för att fråga efter/identifiera resurser i dina prenumerationer. Se till att alla Azure-resurser som finns i miljön godkänns.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en viss resurstyp med Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning:** Använd villkorsstyrd åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "Blockera åtkomst" för appen "Microsoft Azure management".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security Benchmark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning:** Använd Azure Policy alias i namnområdet "Microsoft.Sql" för att skapa anpassade principer för att granska eller framtvinga konfigurationen av resurser som är relaterade till din dedikerade SQL-pool. Du kan också använda inbyggda principdefinitioner för Azure-databaser/Server, till exempel:

- Distribuera hotidentifiering på SQL-servrar
- SQL Server bör använda en tjänstslutpunkt för virtuellt nätverk

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Underhålla säkra Azure-resurskonfigurationer

**Vägledning:** Använd Azure Policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar för alla dina Azure-resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Förstå Azure Policy effekter](../governance/policy/concepts/effects.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Lagra konfiguration av Azure-resurser på ett säkert sätt

**Vägledning:** Om du använder anpassade Azure Policy bör du använda Azure DevOps eller Azure Repos för att lagra och hantera din kod på ett säkert sätt.

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Dokumentation om Azure Repos](/azure/devops/repos/)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuera konfigurationshanteringsverktyg för Azure-resurser

**Vägledning:** Inte tillämpligt; Azure Synapse SQL har inte konfigurerbara säkerhetsinställningar.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementera automatiserad konfigurationsövervakning för Azure-resurser

**Vägledning:** Använd Azure Security Center för att utföra baslinjesökningar för alla resurser som är relaterade till din dedikerade SQL-pool.

- [Så här åtgärdar du rekommendationer i Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning:** transparent datakryptering (TDE) med kund hanterade nycklar i Azure Key Vault tillåter kryptering av den automatiskt genererade databaskrypteringsnyckeln (DEK) med en kund hanterad asymmetrisk nyckel som kallas TDE-skydd. Detta kallas även för BYOK-stöd (Bring Your Own Key) för transparent datakryptering. I BYOK-scenariot lagras TDE-skyddet i ett kundägt och hanterat Azure Key Vault. Kontrollera dessutom att mjuk borttagning är aktiverat i Azure Key Vault.

- [Så här aktiverar du TDE med kund hanterad nyckel från Azure Key Vault](../azure-sql/database/transparent-data-encryption-byok-configure.md)

- [Så här aktiverar du mjuk borttagning i Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Hantera identiteter på ett säkert och automatiskt sätt

**Vägledning:** Använda hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Med hanterade identiteter kan du autentisera till alla tjänster som stöder Azure AD-autentisering, Azure Key Vault, utan några autentiseringsuppgifter i koden.

- [Självstudie: Använda en systemtilldelad hanterad identitet för en virtuell Windows-dator för åtkomst till Azure SQL](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)

- [Så här konfigurerar du hanterade identiteter](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

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

**Vägledning:** Microsofts program mot skadlig kod är aktiverat på den underliggande värd som stöder Azure-tjänster (till exempel Azure Synapse SQL); Men den körs inte på kundinnehåll.

Försök allt innehåll som överförs till Icke-beräkningsbaserade Azure-resurser, till exempel App Service, Data Lake Storage, Blob Storage, Azure SQL Server osv. Microsoft kan inte komma åt dina data i dessa instanser.

- [Förstå Microsoft Antimalware för Azure Cloud Services och Virtual Machines](../security/fundamentals/antimalware.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Prestandatest för Azure-säkerhet: Dataåterställning.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Säkerställ regelbundna automatiserade säkerhetskopieringar

**Vägledning:** Ögonblicksbilder av din dedikerade SQL-pool tas automatiskt under dagen och skapar återställningspunkter som är tillgängliga i sju dagar. Den här kvarhållningsperioden kan inte ändras. Dedikerad SQL-pool stöder ett mål för återställningspunkt på åtta timmar (RPO). Du kan återställa informationslagret i den primära regionen från någon av de ögonblicksbilder som tagits under de senaste sju dagarna. Observera att du även kan utlösa ögonblicksbilder manuellt om det behövs.

- [Säkerhetskopiera och återställa i dedikerad SQL-pool](sql-data-warehouse/backup-and-restore.md)

**Ansvar**: Delad

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utför fullständiga systemsäkerhetskopior och säkerhetskopiera kund hanterade nycklar

**Vägledning:** Ögonblicksbilder av informationslagret tas automatiskt under dagen och skapar återställningspunkter som är tillgängliga i sju dagar. Den här kvarhållningsperioden kan inte ändras. Dedikerad SQL-pool stöder ett mål för återställningspunkt på åtta timmar (RPO). Du kan återställa informationslagret i den primära regionen från någon av de ögonblicksbilder som tagits under de senaste sju dagarna. Observera att du även kan utlösa ögonblicksbilder manuellt om det behövs.

Om du använder en kund hanterad nyckel för att kryptera din databaskrypteringsnyckel ska du se till att nyckeln säkerhetskopieras.

- [Säkerhetskopiera och återställa i dedikerad SQL-pool](sql-data-warehouse/backup-and-restore.md)

- [Säkerhetskopiera Azure Key Vault nycklar](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Ansvar**: Delad

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Verifiera alla säkerhetskopior, inklusive kund hanterade nycklar

**Vägledning:** Testa dina återställningspunkter regelbundet för att säkerställa att dina ögonblicksbilder är giltiga. Om du vill återställa en befintlig dedikerad SQL-pool från en återställningspunkt kan du använda antingen Azure Portal eller PowerShell. Testa återställning av säkerhetskopierade kund hanterade nycklar.

- [Återställa Azure Key Vault nycklar](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Säkerhetskopiera och återställa i dedikerad SQL-pool](sql-data-warehouse/backup-and-restore.md)

- [Återställa en befintlig dedikerad SQL-pool](sql-data-warehouse/sql-data-warehouse-restore-active-paused-dw.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Skydda säkerhetskopior och kund hanterade nycklar

**Vägledning:** I Azure SQL Database kan du konfigurera en enkel databas eller en pooldatabas med en princip för långsiktig kvarhållning av säkerhetskopior (LTR) för att automatiskt behålla databassäkerhetskopiorna i separata Azure Blob Storage-containrar i upp till 10 år. Data i Azure Storage krypteras och dekrypteras transparent med 256-bitars AES-kryptering, ett av de starkaste blockchiffreringarna som är tillgängliga och är FIPS 140-2-kompatibel.

Som standard krypteras data i ett lagringskonto med Microsoft-hanterade nycklar. Du kan använda Microsoft-hanterade nycklar för kryptering av dina data, eller så kan du hantera kryptering med dina egna nycklar. Om du hanterar dina egna nycklar med Key Vault ska du se till att mjuk borttagning är aktiverat.

- [Hantera Azure SQL Database långsiktig kvarhållning av säkerhetskopior](../azure-sql/database/long-term-backup-retention-configure.md)

- [Azure Storage-kryptering av vilande data](../storage/common/storage-service-encryption.md)

- [Så här aktiverar du mjuk borttagning i Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Skapa en guide för incidentsvar

**Vägledning:** Se till att det finns skriftliga planer för incidenthantering som definierar roller för personal samt faser för incidenthantering/incidenthantering.

- [Så här konfigurerar du arbetsflödesautomation inom Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Skapa en procedur för incidentbedömning och prioritering

**Vägledning:** Security Center tilldelar aviseringar en allvarlighetsgrad för att hjälpa dig att prioritera i vilken ordning du deltar i varje avisering, så att när en resurs komprometteras kan du komma åt den direkt. Allvarlighetsgraden baseras på hur säker Security Center är i hitta eller måttet som används för att utfärda aviseringen samt konfidensnivån för att det fanns skadlig avsikt bakom aktiviteten som ledde till aviseringen.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="103-test-security-response-procedures"></a>10.3: Testa procedurer för säkerhetssvar

**Vägledning:** Genomför övningar för att regelbundet testa systemets funktioner för incidenter. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [Du kan referera till NIST:s publikation: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Guide till test-, tränings- och övningsprogram för IT-planer och IT-funktioner)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Ange kontaktinformation om säkerhetsincidenter och konfigurera aviseringsmeddelanden för säkerhetsincidenter

**Vägledning:** Kontaktinformation om säkerhetsincident används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en obehörig eller obehörig part.

- [Så här anger du Azure Security Center säkerhetskontakt](../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Införliva säkerhetsaviseringar i systemet för incidentsvar

**Vägledning:** Exportera dina Azure Security Center och rekommendationer med hjälp av funktionen Kontinuerlig export. Med löpande export kan du exportera aviseringar och rekommendationer manuellt eller kontinuerligt. Du kan använda anslutningsappen Azure Security Center data för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du löpande export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisera svaret på säkerhetsaviseringar

**Vägledning:** Använd funktionen Arbetsflödesautomation i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" om säkerhetsaviseringar och rekommendationer.

- [Så här konfigurerar du arbetsflödesautomation och Logic Apps](../security-center/workflow-automation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [Azure Security Benchmark: Penetration Tests (Intrångstester) och Red Team Exercises (Red Team-övningar).](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Genomför regelbundna intrångstester av dina Azure-resurser och se till att alla viktiga säkerhetsresultat åtgärdas

**Vägledning:** Följ Microsofts regler för engagemang för att säkerställa att dina intrångstester inte bryter mot Microsofts principer: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 .

- [Du hittar mer information om Microsofts strategi och körning av Red Teaming och intrångstestning av live-webbplatser mot Microsofts hanterade molninfrastruktur, tjänster och program här](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
