---
title: Azures säkerhets bas linje för Azure Synapse Analytics
description: Säkerhets bas linjen för Synapse Analytics ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: synapse-analytics
ms.subservice: security
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 65985e05be1061bb69eb160cf5be559cb271d5cd
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202937"
---
# <a name="azure-security-baseline-for-azure-synapse-analytics"></a>Azures säkerhets bas linje för Azure Synapse Analytics

Azures säkerhets bas linje för Azure Synapse Analytics innehåller rekommendationer som hjälper dig att förbättra distributionens säkerhets position.

Bas linjen för den här tjänsten hämtas från [Azures prestandatest version 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview), som ger rekommendationer om hur du kan skydda dina moln lösningar i Azure med våra bästa praxis rikt linjer.

Mer information finns i [Översikt över Azure Security-bas linjer](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets kontroll: nätverks säkerhet](/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: skydda Azure-SQL Server till ett virtuellt nätverk via en privat länk. Med Azures privata länk kan du få åtkomst till Azure PaaS-tjänster via en privat slut punkt i det virtuella nätverket. Trafik mellan ditt virtuella nätverk och tjänsten flyttar Microsoft stamnät nätverket.

När du ansluter till din Synapse SQL-pool begränsar du den utgående anslutningens omfattning till SQL-databasen med hjälp av en nätverks säkerhets grupp. Inaktivera all Azure-tjänstetrafik till SQL-databasen via den offentliga slut punkten genom att ställa in Tillåt att Azure-tjänster STÄNGs av. Se till att inga offentliga IP-adresser tillåts i brand Väggs reglerna.

* [Förstå privat Azure-länk](https://docs.microsoft.com/azure/private-link/private-link-overview)

* [Förstå privat länk för Azure Synapse SQL](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)

* [Så här skapar du en Virtual Network](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Så här skapar du en NSG med en säkerhets konfiguration](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverks gränssnitt

**Vägledning**: när du ansluter till din dedikerade SQL-pool och du har aktiverat flödes loggar för nätverks säkerhets grupper (NSG) skickas loggar till ett Azure Storage konto för trafik granskning.

Du kan också skicka NSG Flow-loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflöde i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

* [Så här aktiverar du NSG Flow-loggar](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Förstå nätverks säkerhet som tillhandahålls av Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [Så här aktiverar och använder du Trafikanalys](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Förstå nätverks säkerhet som tillhandahålls av Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för Azure Apps-tjänster eller data bearbetnings resurser som är värdar för webb program.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Använd Advanced Threat Protection (ATP) för Azure Synapse SQL. ATP identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser och kan utlösa olika aviseringar, till exempel "potentiell SQL-inmatning" och "åtkomst från ovanlig plats". ATP är en del av erbjudandet för avancerad data säkerhet (ADS) och kan nås och hanteras via den centrala SQL ADS-portalen.

Aktivera DDoS Protection standard på de virtuella nätverk som är kopplade till Azure Synapse SQL för skydd från distribuerade DOS-attacker (Denial-of-Service). Använd Azure Security Center integrerad Hot information för att neka kommunikation med kända skadliga eller oanvända Internet-IP-adresser.

* [Förstå ATP för Azure Synapse SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

* [Så här aktiverar du avancerad data säkerhet för Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [Översikt över ADS](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [Så här konfigurerar du DDoS-skydd](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Förstå Azure Security Center integrerad Hot information](https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets"></a>1,5: registrera nätverks paket

**Vägledning**: när du ansluter till din dedikerade SQL-pool och du har aktiverat flödes loggar för nätverks säkerhets grupper (NSG) skickar du loggar till ett Azure Storage konto för trafik granskning. Du kan också skicka flödes loggar till en Log Analytics arbets yta eller strömma dem till Event Hubs. Om det behövs för att undersöka avvikande aktivitet aktiverar du Network Watcher paket fångst.

* [Så här aktiverar du NSG Flow-loggar](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Så här aktiverar du Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera nätverksbaserade intrångs identifiering/system för skydd mot intrång (ID/IP-adresser)

**Vägledning**: Använd Advanced Threat Protection (ATP) för Azure Synapse SQL. ATP identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser och kan utlösa olika aviseringar, till exempel "potentiell SQL-inmatning" och "åtkomst från ovanlig plats". ATP är en del av erbjudandet för avancerad data säkerhet (ADS) och kan nås och hanteras via den centrala SQL ADS-portalen. ATP integrerar även aviseringar med Azure Security Center.

* [Förstå ATP för Azure Synapse SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för Azure Apps-tjänster eller data bearbetnings resurser som är värdar för webb program.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: Använd taggar för virtuella nätverks tjänster för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper eller Azure-brandvägg. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet (t. ex. API Management) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

När du använder en tjänst slut punkt för den dedikerade SQL-poolen krävs utgående till Azure SQL Database-offentliga IP-adresser: nätverks säkerhets grupper (NSG: er) måste öppnas för att Azure SQL Database IP-adresser ska kunna ansluta. Du kan göra detta med hjälp av NSG service-taggar för Azure SQL Database.

* [Förstå service märken med tjänst slut punkter för Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations)

* [Förstå och använda service märken](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera konfigurationer för nätverks säkerhet för resurser som är relaterade till din DEDIKERADe SQL-pool med Azure policy. Du kan använda namn området "Microsoft. SQL" för att definiera anpassade princip definitioner eller använda någon av de inbyggda princip definitionerna som är utformade för Azure SQL Database/Server nätverks skydd. Ett exempel på en tillämplig inbyggd nätverks säkerhets princip för Azure SQL Database Server är: "SQL Server bör använda en tjänst slut punkt för virtuellt nätverk".

Använd Azure-ritningar för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljö artefakter, till exempel Azure Resource Management-mallar, rollbaserad åtkomst kontroll i Azure (Azure RBAC) och principer, i en enda skiss definition. Använd enkelt skissen på nya prenumerationer och miljöer och finjustera kontroll och hantering genom versions hantering.

* [Konfigurera och hantera Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Så här skapar du en Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för nätverks säkerhets grupper (NSG) och andra resurser som rör nätverks säkerhets-och trafikflödet. För enskilda NSG-regler använder du fältet Beskrivning för att ange affärs behov och/eller varaktighet (osv.) för alla regler som tillåter trafik till/från ett nätverk.

Använd någon av de inbyggda Azure Policy definitionerna som är relaterade till taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och meddela dig om befintliga otaggade resurser.

Du kan använda Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

* [Skapa och använda Taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar för nätverks resurser som är relaterade till din DEDIKERADe SQL-pool. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks resurser sker.

* [Visa och hämta Azure aktivitets logg händelser](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Så här skapar du aviseringar i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets kontroll: loggning och övervakning](/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

**Vägledning**: Microsoft hanterar tids källor för Azure-resurser. Du kan uppdatera tidssynkroniseringen för dina beräknings distributioner.

* [Så här konfigurerar du tidssynkronisering för Azure Compute-resurser](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: en gransknings princip kan definieras för en viss databas eller som en standard server princip i Azure (som är värd för Azure-Synapse). En server princip gäller för alla befintliga och nyligen skapade databaser på servern.

Om Server granskning är aktiverat gäller det alltid för-databasen. Databasen kommer att granskas, oavsett databas gransknings inställningar.

När du aktiverar granskning kan du skriva dem till en Gransknings logg i ditt Azure Storage-konto, Log Analytics arbets ytan eller Event Hubs.

Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM från tredje part.

* [Konfigurera granskning för dina Azure SQL-resurser](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

* [Publicera Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: aktivera granskning på Azure SQL Server-nivån för din DEDIKERADe SQL-pool och välj en lagrings plats för gransknings loggarna (Azure Storage, Log Analytics eller Event Hubs).

Granskning kan aktive ras både på databas-eller server nivå och rekommenderas bara att aktive ras på server nivå, om du inte behöver konfigurera en separat data mottagare eller kvarhållning för en särskild databas.

* [Så här aktiverar du granskning för Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing)

* [Så här aktiverar du granskning för servern](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#setup-auditing)

* [Skillnader i Server nivå kontra gransknings principer på databas nivå](https://docs.microsoft.com/azure/sql-database/sql-database-auditing#server-vs-database-level)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

**Vägledning**: ej tillämpligt; Detta riktmärke är avsett för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: när du lagrar loggar som är relaterade till din dedikerade SQL-pool i ett lagrings konto, Log Analytics arbets yta eller Event Hub, ställer du in logg kvarhållningsperiod enligt organisationens regler för efterlevnad.

* [Hantera Azure Blob Storage-livscykeln](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)

* [Så här ställer du in logg behållar parametrar i en Log Analytics-arbetsyta](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

* [Avbilda strömmande händelser i Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: analysera och övervaka loggar för avvikande beteenden och granska resultaten regelbundet. Använd avancerat skydd för Azure SQL Database tillsammans med Azure Security Center för att varna vid ovanlig aktivitet som är relaterad till din SQL-databas. Du kan också konfigurera aviseringar baserat på mått värden eller Azures aktivitets logg poster som är relaterade till din SQL-databas.

Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM från tredje part.

* [Förstå Avancerat skydd och aviseringar för Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

* [Så här aktiverar du avancerad data säkerhet för Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [Konfigurera anpassade aviseringar för Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0&preserve-view=true)

* [Publicera Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: Använd avancerat skydd (ATP) för Azure SQL Database tillsammans med Azure Security Center för att övervaka och varna för avvikande aktivitet. ATP är en del av erbjudandet för avancerad data säkerhet (ADS) och kan nås och hanteras via centrala SQL-annonser i portalen. ADS innehåller funktioner för att identifiera och klassificera känsliga data, Visa och minimera potentiella databas sårbarheter och upptäcka avvikande aktiviteter som kan tyda på ett hot mot databasen.

Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel.

* [Förstå Avancerat skydd och aviseringar för Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

* [Så här aktiverar du avancerad data säkerhet för Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [Hantera aviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

* [Publicera Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: ej tillämpligt; för resurser som är relaterade till din dedikerade SQL-pool hanteras lösningen mot skadlig kod av Microsoft på den underliggande plattformen.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: ej tillämpligt; inga DNS-loggar skapas av resurser som är relaterade till din dedikerade SQL-pool.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

**Vägledning**: ej tillämpligt; granskning av kommando tolken gäller inte för Azure Synapse SQL.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [säkerhets kontroll: identitets-och åtkomst kontroll](/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: användarna autentiseras med antingen Azure Active Directory eller SQL-autentisering.

När du först distribuerar Azure SQL anger du en Administratörs inloggning och ett kopplat lösen ord för inloggningen. Det här administratörs kontot kallas Server administratör. Du kan identifiera administratörs konton för en databas genom att öppna Azure Portal och navigera till fliken Egenskaper för servern eller den hanterade instansen. Du kan också konfigurera ett administratörs konto för Azure AD med fullständig administratörs behörighet, vilket krävs om du vill aktivera Azure Active Directory autentisering.

För hanterings åtgärder använder du de inbyggda Azure-rollerna som måste tilldelas explicit. Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

* [Autentisering för SQL Database](https://docs.microsoft.com/azure/azure-sql/database/security-overview#authentication)

* [Skapa konton för icke-administrativa användare](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-accounts-for-non-administrator-users)

* [Använd ett Azure Active Directory konto för autentisering](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-additional-logins-and-users-having-administrative-permissions)

* [Så här hämtar du en katalog roll i Azure AD med PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&preserve-view=true)

* [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&preserve-view=true)

* [Hantera befintliga inloggningar och administratörs konton i Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

* [Inbyggda roller i Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: Azure Active Directory saknar begreppet standard lösen ord. När du konfigurerar en dedikerad SQL-pool rekommenderar vi att du väljer att integrera autentisering med Azure Active Directory. Med den här autentiseringsmetoden skickar användaren ett användar konto namn och begär att tjänsten använder den autentiseringsinformation som lagrats i Azure Active Directory (Azure AD).

* [Konfigurera och hantera Azure Active Directory autentisering med Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure?tabs=azure-powershell#active-directory-password-authentication)

* [Förstå autentisering i Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa principer och procedurer kring användningen av dedikerade administrativa konton. Använd Azure Security Center identitets-och åtkomst hantering för att övervaka antalet administrativa konton som loggar in via Azure Active Directory.

Om du vill identifiera administratörs konton för en databas öppnar du Azure Portal och navigerar till fliken Egenskaper för servern eller den hanterade instansen.

* [Förstå Azure Security Center identitet och åtkomst](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Hantera befintliga inloggningar och administratörs konton i Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Använd Azure Active Directory enkel inloggning (SSO)

**Vägledning**: Använd en Azure App-registrering (tjänstens huvud namn) för att hämta en token som kan användas för att interagera med ditt informations lager i kontroll planet (Azure Portal) via API-anrop.

* [Så här anropar du Azure REST API: er](https://docs.microsoft.com/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [Registrera klient programmet (tjänstens huvud namn) med Azure AD](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Information om Azure Synapse SQL-REST API](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory-baserad åtkomst

**Vägledning**: Aktivera Azure Active Directory (AD) Multi-Factor Authentication (MFA) och följ rekommendationer för Azure Security Center identitets-och åtkomst hantering.

* [Aktivera MFA i Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Övervaka identitet och åtkomst i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Förstå MFA i Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/authentication-mfa-ssms-overview)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Använd säkra, Azure-hanterade arbets stationer för administrativa uppgifter

**Vägledning**: Använd en privilegie rad åtkomst arbets Station (Paw) med Multi-Factor Authentication (MFA) som kon figurer ATS för att logga in på och konfigurera Azure-resurser.

* [Lär dig mer om arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Aktivera MFA i Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: Använd Azure Active Directory säkerhets rapporter för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön.

Använd avancerat skydd för Azure SQL Database tillsammans med Azure Security Center för att identifiera och Varna vid avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

Med SQL Server granskning kan du skapa server granskningar, som kan innehålla Server gransknings specifikationer för händelser på server nivå och databas gransknings specifikationer för händelser på databas nivå. Granskade händelser kan skrivas till händelse loggarna eller för att granska filer.

* [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Övervaka användarnas identitets-och åtkomst aktiviteter i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Granska Avancerat skydd och potentiella aviseringar](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts)

* [Förstå inloggningar och användar konton i Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

* [Förstå SQL Server granskning](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-ver15&preserve-view=true)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: använda villkorlig åtkomst med namngivna platser för att tillåta åtkomst till Portal-och Azure-resurser enbart för vissa logiska grupperingar av IP-adressintervall eller länder/regioner.

* [Så här konfigurerar du namngivna platser i Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: skapa en Azure Active Directory-administratör (AD) för Azure SQL Database-servern i din DEDIKERADe SQL-pool.

* [Så här konfigurerar och hanterar du Azure AD-autentisering med Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)

* [Skapa och konfigurera en Azure AD-instans](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure Active Directory innehåller loggar som hjälper dig att identifiera inaktuella konton. Använd dessutom Azure Active Directory åtkomst granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användarnas åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

När du använder SQL-autentisering skapar du inneslutna databas användare i databasen. Se till att du placerar en eller flera databas användare i en anpassad databas roll med vissa behörigheter som är lämpliga för den gruppen av användare.

* [Så här använder du åtkomst granskningar](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Förstå inloggningar och användar konton i Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: Konfigurera Azure Active Directory (AD)-autentisering med Azure SQL och aktivera diagnostikinställningar för Azure Active Directory användar konton, skicka gransknings loggar och inloggnings loggar till en Log Analytics-arbetsyta. Konfigurera önskade aviseringar inom Log Analytics.

När du använder SQL-autentisering skapar du inneslutna databas användare i databasen. Se till att du placerar en eller flera databas användare i en anpassad databas roll med vissa behörigheter som är lämpliga för den gruppen av användare.

* [Så här använder du åtkomst granskningar](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Så här konfigurerar och hanterar du Azure AD-autentisering med Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)

* [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [Förstå inloggningar och användar konton i Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: varning vid inloggnings beteende för konto

**Vägledning**: Använd Azure Active Directory (Azure AD) identitets skydd och funktioner för identifiering av risker för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter. Dessutom kan du använda data på kort och mata in i Azure Sentinel för ytterligare undersökning.

När du använder SQL-autentisering skapar du inneslutna databas användare i databasen. Se till att du placerar en eller flera databas användare i en anpassad databas roll med vissa behörigheter som är lämpliga för den gruppen av användare.

* [Visa inloggnings program för Azure AD-risk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Så här konfigurerar och aktiverar du risk principer för identitets skydd](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Aktivera Azure-kontroll på kort](https://docs.microsoft.com/azure/sentinel/connect-data-sources)

* [Förstå inloggningar och användar konton i Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: i support scenarier där Microsoft behöver åtkomst till data som är relaterade till Azure SQL Database i din dedikerade SQL-pool tillhandahåller Azure Customer lockbox ett gränssnitt som du kan använda för att granska och godkänna eller avvisa begär Anden om data åtkomst.

* [Förstå Customer Lockbox](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [säkerhets kontroll: data skydd](/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information.

Klassificering av data identifiering &amp; är inbyggt i Azure SYNAPSE SQL. Den här funktionen ger avancerade funktioner för identifiering, klassificering, märkning och rapportering av känsliga data i databasen.

* [Skapa och använda Taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Förstå klassificering av data identifiering &amp;](https://docs.microsoft.com/azure/azure-sql/database/data-discovery-and-classification-overview)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera separata prenumerationer och/eller hanterings grupper för utveckling, testning och produktion. Resurser bör åtskiljas av ett virtuellt nätverk/undernät, taggas på lämpligt sätt och skyddas inom en nätverks säkerhets grupp eller Azure-brandvägg. Resurser som lagrar eller bearbetar känsliga data bör isoleras. Använd privat länk; Distribuera Azure SQL Server i ett virtuellt nätverk och anslut säkert med privat länk.

* [Så här skapar du ytterligare Azure-prenumerationer](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Så här skapar du Hanteringsgrupper](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Skapa och använda Taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Så här konfigurerar du en privat länk för Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: för alla Azure SQL Database i din dedikerade SQL-pool lagrar eller bearbetar känslig information, markerar du databasen och relaterade resurser som känsliga med taggar. Konfigurera en privat länk tillsammans med nätverks säkerhets gruppens (NSG) service-taggar på dina Azure SQL Database instanser för att förhindra exfiltrering av känslig information.

Dessutom identifierar Avancerat skydd för Azure SQL Database, Azure SQL-hanterad instans och Azure-Synapse avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

* [Så här konfigurerar du privat länk-och NSG: er för att förhindra data exfiltrering på Azure SQL Database instanser](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)

* [Förstå Avancerat skydd för Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview)

* [Förstå skydd av kunddata i Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: Azure SQL Database skyddar dina data genom att kryptera data i rörelse med Transport Layer Security. SQL Server använder kryptering (SSL/TLS) hela tiden för alla anslutningar. Detta säkerställer att alla data krypteras "under överföring" mellan klienten och servern oavsett inställningen för kryptering eller TrustServerCertificate i anslutnings strängen.

* [Förstå Azure SQL-kryptering under överföring](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: Använd klassificerings funktionen i Azure Synapse SQL data Discovery &amp; . &amp;Klassificering av data identifiering ger avancerade funktioner som är inbyggda i Azure SQL Database för att upptäcka, klassificera och märka &amp; att skydda känsliga data i dina databaser.

&amp;Klassificering av data identifiering är en del av det avancerade data säkerhets erbjudandet, som är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Data identifierings &amp; klassificering kan nås och hanteras via den centrala SQL Ads-portalen.

Dessutom kan du konfigurera en princip för dynamisk data maskering (DDM) i Azure Portal. DDM rekommendationer-motorn flaggar vissa fält från databasen som potentiellt känsliga fält som kan vara bra att maskera.

* [Så här använder du data identifiering och klassificering för Azure SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

* [Förstå dynamisk data maskning för Azure Synapse SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser

**Vägledning**: Använd rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att hantera åtkomst till Azure SQL-databaser i din DEDIKERADe SQL-pool.

Auktoriseringen styrs av ditt användar kontos databas roll medlemskap och behörigheter på objekt nivå. Ett bra tips är att du ska ge användare så få behörigheter som möjligt.

* [Så här integrerar du Azure-SQL Server med Azure Active Directory för autentisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)

* [Så här kontrollerar du åtkomst i Azure SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-control-access)

* [Förstå auktorisering och autentisering i Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: ej tillämpligt; Microsoft hanterar den underliggande infrastrukturen för Azure Synapse SQL och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information.

* [Förstå skydd av kunddata i Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: transparent data kryptering (TDE) hjälper till att skydda Azure Synapse SQL mot hot från skadlig offline-aktivitet genom att kryptera data i vila. TDE utför realtidskryptering och realtidsdekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler i vila, utan att några ändringar krävs i programmet. I Azure är standardinställningen för TDE att DEK skyddas av ett inbyggt Server certifikat. Du kan också använda Kundhanterade TDE, även kallat Bring Your Own Key (BYOK) stöd för TDE. I det här scenariot är TDE-skyddskomponenten som krypterar DEK en kundhanterad asymmetrisk nyckel, som lagras i ett kundägda och hanterat Azure Key Vault (Azures molnbaserade externt nyckel hanterings system) och lämnar aldrig nyckel valvet.

* [Förstå hanterad transparent data kryptering i tjänst](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview?tabs=azure-portal)

* [Förstå kundhanterad transparent data kryptering](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview?tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key)

* [Så här aktiverar du TDE genom att använda din egen nyckel](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-byok-configure)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i produktions instanser av Synapse SQL-pooler och andra kritiska eller relaterade resurser.

Dessutom kan du ställa in aviseringar för databaser i SQL Synapse-poolen med hjälp av Azure Portal. Aviseringar kan skicka ett e-postmeddelande till dig eller anropa en webbhook när något mått (till exempel databas storlek eller CPU-användning) når tröskelvärdet.

* [Så här skapar du aviseringar för Azure aktivitets logg händelser](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [Skapa aviseringar för Azure SQL-Synapse](https://docs.microsoft.com/azure/azure-sql/database/alerts-insights-configure-portal)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [säkerhets kontroll: sårbarhets hantering](/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: aktivera avancerad data säkerhet och följ rekommendationer från Azure Security Center om att utföra sårbarhets bedömningar på dina Azure SQL-databaser.

* [Så här kör du sårbarhets bedömningar i Azure SQL-databaser](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)

* [Så här aktiverar du avancerad data säkerhet](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [Så här implementerar du rekommendationer för Azure Security Center sårbarhets bedömning](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Distribuera automatiserad korrigerings hanterings lösning för program varu titlar från tredje part

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: sårbarhets bedömning är en skannings tjänst som är inbyggd i Azure Synapse SQL. Tjänsten använder en kunskaps bas för regler som åtgärdar säkerhets sårbarheter. Den visar avvikelser från bästa praxis, till exempel felkonfigurationer, överdriven behörighet och oskyddade känsliga data. Sårbarhets bedömning kan nås och hanteras via den centrala SQL Advanced Data Security-portalen (ADS).

* [Hantera och exportera sökningar efter sårbarhets bedömningar i SQL ADS-portalen](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: Använd standard risk klassificeringarna (säkra poäng) som tillhandahålls av Azure Security Center.

Klassificering av data identifiering &amp; är inbyggt i Azure SYNAPSE SQL. Den här funktionen ger avancerade funktioner för identifiering, klassificering, märkning och rapportering av känsliga data i databasen.

* [Förstå Azure Security Center säkra Poäng](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

* [Förstå klassificering av data identifiering &amp;](https://docs.microsoft.com/azure/azure-sql/database/data-discovery-and-classification-overview)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [säkerhets kontroll: inventering och till gångs hantering](/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga efter och identifiera alla resurser som är relaterade till din DEDIKERADe SQL-pool i din prenumeration (er). Se till att du har rätt (Läs) behörigheter i din klient och kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via Azure Resource Graph, rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser som går framåt.

* [Så här skapar du frågor med Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Så här visar du dina Azure-prenumerationer](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0&preserve-view=true)

* [Förstå Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser som ger metadata till att logiskt organisera dem i en taxonomi.

* [Skapa och använda Taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer, vid behov, för att organisera och spåra till gångar. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

* [Så här skapar du ytterligare Azure-prenumerationer](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Så här skapar du Hanteringsgrupper](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Skapa och använda Taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla inventering av godkända Azure-resurser

**Vägledning**: definiera en lista över godkända Azure-resurser som är relaterade till din DEDIKERADe SQL-pool.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

Använd Azure Resource Graph för att fråga/identifiera resurser i dina prenumerationer. Se till att alla Azure-resurser som finns i miljön är godkända.

* [Konfigurera och hantera Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Så här skapar du frågor med Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att placera begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

Använd Azure Resource Graph för att fråga/identifiera resurser i dina prenumerationer. Se till att alla Azure-resurser som finns i miljön är godkända.

* [Konfigurera och hantera Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Så här nekar du en speciell resurs typ med Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: underhåll en inventering av godkända program varu titlar

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för program som körs på beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

* [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: alla resurser som är relaterade till din dedikerade SQL-pool som krävs för affärs åtgärder, men som kan innebära högre risk för organisationen, bör isoleras inom den egna virtuella datorn och/eller det virtuella nätverket och tillräckligt säkert med antingen en Azure-brandvägg eller en nätverks säkerhets grupp.

* [Så här skapar du ett virtuellt nätverk](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Så här skapar du en NSG med en säkerhets konfiguration](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [säkerhets kontroll: säker konfiguration](/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. SQL" för att skapa anpassade principer för att granska eller framtvinga konfigurationen av resurser som är relaterade till din DEDIKERADe SQL-pool. Du kan också använda inbyggda princip definitioner för Azure Database/Server, till exempel:
- Distribuera hot identifiering på SQL-servrar
- SQL Server bör använda en tjänst slut punkt för virtuellt nätverk

* [Visa tillgängliga Azure Policy alias](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0&preserve-view=true)

* [Konfigurera och hantera Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar i dina Azure-resurser.

* [Konfigurera och hantera Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Förstå Azure Policys effekter](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: om du använder anpassade Azure policys definitioner använder du Azure DevOps eller Azure databaser för att lagra och hantera din kod på ett säkert sätt.

* [Så här lagrar du kod i Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

* [Dokumentation om Azure databaser](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops&preserve-view=true)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: ej tillämpligt; Azure Synapse SQL har inte konfigurerbara säkerhets inställningar.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Distribuera konfigurations hanterings verktyg för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: utnyttja Azure Security Center för att utföra bas linje genomsökningar för alla resurser som är relaterade till din DEDIKERADe SQL-pool.

* [Så här åtgärdar du rekommendationer i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: transparent DATAKRYPTERING (TDE) med Kundhanterade nycklar i Azure Key Vault gör det möjligt att kryptera den automatiskt genererade databas krypterings nyckeln (DEK) med en kundhanterad asymmetrisk nyckel som kallas TDE-skydd. Detta kallas även Bring Your Own Key (BYOK) stöd för transparent datakryptering. I BYOK-scenariot lagras TDE-skyddet i ett kundägda och hanterat Azure Key Vault. Se dessutom till att mjuk borttagning är aktiverat i Azure Key Vault.

* [Så här aktiverar du TDE med kundhanterad nyckel från Azure Key Vault](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-byok-configure?tabs=azure-powershell)

* [Så här aktiverar du mjuk borttagning i Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory (AD). Med hanterade identiteter kan du autentisera till vilken tjänst som helst som stöder Azure AD-autentisering, inklusive Azure Key Vault utan autentiseringsuppgifter i din kod.

* [Självstudie: Använda en systemtilldelad hanterad identitet för en virtuell Windows-dator för åtkomst till Azure SQL](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql)

* [Konfigurera hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifter för inloggning för att identifiera autentiseringsuppgifter i din kod. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault.

* [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [säkerhets kontroll: försvar mot skadlig kod](/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Använd en centralt hanterad program vara mot skadlig kod

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser. Microsoft hanterar skydd mot skadlig kod för den underliggande plattformen.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som har stöd för Azure-tjänster (till exempel Azure Synapse SQL). den körs dock inte på kund innehåll.

Förskanna allt innehåll som laddas upp till Azure-resurser som inte är Compute, till exempel App Service, Data Lake Storage, Blob Storage, Azure SQL Server osv. Microsoft kan inte komma åt dina data i dessa instanser.

* [Förstå Microsoft Antimalware för Azure Cloud Services och Virtual Machines](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för skadlig program vara uppdateras

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser. Microsoft hanterar skydd mot skadlig kod för den underliggande plattformen.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [säkerhets kontroll: Data återställning](/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Säkerställ regelbunden automatisk säkerhets kopiering

**Vägledning**: ögonblicks bilder av din DEDIKERADe SQL-pool tas automatiskt under dagen som skapar återställnings punkter som är tillgängliga i sju dagar. Kvarhållningsperioden kan inte ändras. Dedikerad SQL-pool har stöd för ett återställnings punkt mål på 8 timmar. Du kan återställa ditt informations lager i den primära regionen från någon av de ögonblicks bilder som tagits under de senaste sju dagarna. Observera att du även kan utlösa ögonblicks bilder manuellt om det behövs.

* [Säkerhets kopiering och återställning i dedikerad SQL-pool](/azure/synapse-analytics/sql-data-warehouse/backup-and-restore)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: ögonblicks bilder av ditt informations lager tas automatiskt under dagen som skapar återställnings punkter som är tillgängliga i sju dagar. Kvarhållningsperioden kan inte ändras. Dedikerad SQL-pool har stöd för ett återställnings punkt mål på 8 timmar. Du kan återställa ditt informations lager i den primära regionen från någon av de ögonblicks bilder som tagits under de senaste sju dagarna. Observera att du även kan utlösa ögonblicks bilder manuellt om det behövs.

Om du använder en kundhanterad nyckel för att kryptera databas krypterings nyckeln, se till att din nyckel säkerhets kopie ras.

* [Säkerhets kopiering och återställning i dedikerad SQL-pool](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/backup-and-restore)

* [Säkerhetskopiera Azure Key Vault nycklar](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0&preserve-view=true)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: testa regelbundet dina återställnings punkter för att se till att dina ögonblicks bilder är giltiga. Om du vill återställa en befintlig dedikerad SQL-pool från en återställnings punkt kan du använda antingen Azure Portal eller PowerShell. Testa återställning av säkerhetskopierade nycklar som hanteras av kunden.

* [Återställa Azure Key Vault nycklar](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0&preserve-view=true)

* [Säkerhets kopiering och återställning i dedikerad SQL-pool](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/backup-and-restore)

* [Återställa en befintlig dedikerad SQL-pool](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-restore-active-paused-dw)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: i Azure SQL Database kan du konfigurera en enskild databas eller en pool med en långsiktig säkerhets kopierings princip (LTR) för att automatiskt behålla databas säkerhets kopiorna i separata Azure Blob Storage-behållare i upp till 10 år. Data i Azure Storage krypteras och dekrypteras transparent med 256-bitars AES-kryptering, en av de starkaste block chiffer som är tillgängliga och är FIPS 140-2-kompatibel.

Som standard krypteras data i ett lagrings konto med Microsoft-hanterade nycklar. Du kan förlita dig på Microsoft-hanterade nycklar för kryptering av dina data, eller så kan du hantera kryptering med dina egna nycklar. Om du hanterar dina egna nycklar med Key Vault se till att mjuk borttagning är aktiverat.

* [Hantera Azure SQL Database långsiktig kvarhållning av säkerhets kopior](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure)

* [Azure Storage-kryptering av vilande data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

* [Så här aktiverar du mjuk borttagning i Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure Security Center övervakning**: ej tillämpligt

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [säkerhets kontroll: incident svar](/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: kontrol lera att det finns skriftliga svars planer för incidenter som definierar personal roller och faser för incident hantering/hantering.

* [Konfigurera automatisering av arbets flöden i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till aviseringar, som hjälper dig att prioritera i vilken ordning du deltar i varje avisering, så att när en resurs komprometteras kan du komma åt den direkt. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

* [Säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems incident svars funktioner på en vanlig takt. Identifiera svaga punkter och luckor, och ändra planen efter behov.

* [Du kan läsa NIST: guide för att testa, träna och träna program för IT-planer och-funktioner](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en olagligt eller obehörig part.

* [Så här ställer du in Azure Security Center säkerhets kontakt](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviseringarna till kontroll.

* [Så här konfigurerar du kontinuerlig export](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Så här strömmar du aviseringar till Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöden i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer.

* [Konfigurera automatisering av arbets flöden och Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstester och Red Team-tester

*Mer information finns i [säkerhets kontroll: inträngande tester och röda team övningar](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: * [Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

* [Du hittar mer information om Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft Managed Cloud Infrastructure, tjänster och program](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
