---
title: Azures säkerhets bas linje
description: Azures säkerhets bas linje för Azure SQL Database och Azure SQL-hanterad instans
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 12c7fd1d8ee36b562cd651f50fd0565825441883
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197395"
---
# <a name="azure-security-baseline-for-azure-sql-database--sql-managed-instance"></a>Azures säkerhets bas linje för Azure SQL Database & SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azures säkerhets bas linje för Azure SQL Database innehåller rekommendationer som hjälper dig att förbättra säkerhets position för din distribution.

Bas linjen för den här tjänsten hämtas från [Azures prestandatest version 1,0](../../security/benchmarks/overview.md), som ger rekommendationer om hur du kan skydda dina moln lösningar i Azure med våra bästa praxis rikt linjer.

Mer information finns i [Översikt över Azure Security-bas linjer](../../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets kontroll: nätverks säkerhet](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: skydda resurser med hjälp av nätverks säkerhets grupper eller Azure-brandvägg på din Virtual Network

**Vägledning**: du kan aktivera Azures privata länk för att tillåta åtkomst till Azure PaaS Services (till exempel SQL Database) och Azure-värdbaserade kund-/partner tjänster via en privat slut punkt i det virtuella nätverket. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet.

Om du vill tillåta att trafik når Azure SQL Database använder du SQL-tjänstens taggar för att tillåta utgående trafik via nätverks säkerhets grupper.

Regler för virtuella nätverk gör det möjligt för Azure SQL Database att endast acceptera kommunikation som skickas från valda undernät i ett virtuellt nätverk.

Så här konfigurerar du en privat länk för Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

Använda tjänst slut punkter och regler för virtuella nätverk för servrar:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverkskort

**Vägledning**: Använd Azure Security Center och åtgärda rekommendationer för nätverks skydd för under nätet som Azure SQL Database distribueras till.

För Azure Virtual Machines (VM) som ska ansluta till din Azure SQL Database aktiverar du flödes loggar för nätverks säkerhets grupper (NSG) för NSG: er som skyddar de virtuella datorerna och skickar loggar till ett Azure Storage konto för trafik granskning.

Du kan också skicka NSG Flow-loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflöde i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

Så här aktiverar du NSG Flow-loggar:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Förstå nätverks säkerhet som tillhandahålls av Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

Så här aktiverar och använder du Trafikanalys:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Förstå nätverks säkerhet som tillhandahålls av Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för Azure Apps-tjänster eller data bearbetnings resurser som är värdar för webb program.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Aktivera DDoS Protection standard på de virtuella nätverk som är kopplade till din Azure SQL Database för skydd från distribuerade DOS-attacker (Denial-of-Service). Använd Azure Security Center integrerad Hot information för att neka kommunikation med kända skadliga eller oanvända Internet-IP-adresser.

Så här konfigurerar du DDoS-skydd:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Förstå Azure Security Center integrerad Hot information:

https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: registrera nätverks paket och flödes loggar

**Vägledning**: för Azure Virtual Machines (VM) som ska ansluta till din Azure SQL Database-instans aktiverar du NSG-flöden (Network Security Group) för NSG: er som skyddar de virtuella datorerna och skickar loggar till ett Azure Storage konto för trafik granskning. Om det behövs för att undersöka avvikande aktivitet aktiverar du Network Watcher paket fångst.

Så här aktiverar du NSG Flow-loggar:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Så här aktiverar du Network Watcher:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera Network-baserad intrångs identifiering/intrångs skydd system (ID/IP-adresser)

**Vägledning**: Aktivera avancerat skydd (ATP) för Azure SQL Database.  Användare får en avisering om misstänkt databas aktiviteter, potentiella sårbarheter och SQL-injektering, samt avvikande databas åtkomst och frågor. Avancerat skydd integrerar även aviseringar med Azure Security Center.

Förstå och använda avancerat skydd för Azure SQL Database: https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för Azure Apps-tjänster eller data bearbetnings resurser som är värdar för webb program.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: Använd taggar för virtuella nätverks tjänster för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper eller Azure-brandvägg. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet (t. ex. API Management) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

När du använder tjänst slut punkter för Azure SQL Database krävs utgående till Azure SQL Database offentliga IP-adresser: nätverks säkerhets grupper (NSG: er) måste öppnas för att det ska vara möjligt att ansluta till Azure SQL Database IP-adresser. Du kan göra detta med hjälp av NSG service-taggar för Azure SQL Database.

Förstå service märken med tjänst slut punkter för Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations

Förstå och använda service märken:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera nätverks säkerhets inställningar för dina Azure SQL Database med Azure policy. Du kan använda namn området "Microsoft. SQL" för att definiera anpassade princip definitioner eller använda någon av de inbyggda princip definitionerna som är utformade för Server nätverks skydd. Ett exempel på en tillämplig inbyggd nätverks säkerhets princip för en server är: "SQL Database bör använda en tjänst slut punkt för virtuellt nätverk".

Använd Azure-ritningar för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljö artefakter, till exempel Azure Resource Management-mallar, rollbaserad åtkomst kontroll i Azure (Azure RBAC) och principer, i en enda skiss definition. Använd enkelt skissen på nya prenumerationer och miljöer och finjustera kontroll och hantering genom versions hantering.

Så här konfigurerar och hanterar du Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här skapar du en Azure Blueprint: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för nätverks säkerhets grupper (NSG) och andra resurser som rör nätverks säkerhets-och trafikflödet. För enskilda NSG-regler använder du fältet Beskrivning för att ange affärs behov och/eller varaktighet (osv.) för alla regler som tillåter trafik till/från ett nätverk.

Använd någon av de inbyggda Azure Policy definitionerna som är relaterade till taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och meddela dig om befintliga otaggade resurser.

Du kan använda Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

Skapa och använda Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar för nätverks resurser som är relaterade till din server. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks resurser sker.

Så här visar och hämtar du Azure aktivitets logg händelser:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Så här skapar du aviseringar i Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets kontroll: loggning och övervakning](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

**Vägledning**: Microsoft hanterar tids källor för Azure-resurser. Du kan uppdatera tidssynkroniseringen för dina beräknings distributioner.

Så här konfigurerar du tidssynkronisering för Azure Compute-resurser:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: aktivera granskning för Azure SQL Database du spårar databas händelser och skriver dem till en Gransknings logg i ditt Azure Storage-konto, Log Analytics arbets yta eller Event Hubs.

Dessutom kan du strömma Azure SQL Diagnostics-telemetri till Azure SQL-analys, en moln lösning som övervakar prestanda för Azure SQL Database och Azure SQL-hanterad instans i skala och över flera prenumerationer. Det kan hjälpa dig att samla in och visualisera Azure SQL Database prestanda mått och har inbyggd intelligens för fel sökning av prestanda.

Konfigurera granskning för Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

Samla in plattforms loggar och-mått med Azure Monitor:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging

Så här strömmar du diagnostik till Azure SQL-analys:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging#stream-into-azure-sql-analytics

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: aktivera granskning på servern och välj en lagrings plats för gransknings loggarna (Azure Storage, Log Analytics eller händelsehubben).

Så här aktiverar du granskning för Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

**Vägledning**: ej tillämpligt; Detta riktmärke är avsett för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: när du lagrar Azure SQL Database loggar på en Log Analytics arbets yta anger du logg kvarhållningsperiod enligt organisationens regler för efterlevnad.

Så här ställer du in parametrar för logg kvarhållning:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: analysera och övervaka loggar för avvikande beteenden och granska resultaten regelbundet. Använd Azure Security Centers avancerade hot skydd för att varna vid ovanlig aktivitet som är relaterad till din Azure SQL Database instans. Du kan också konfigurera aviseringar baserat på mått värden eller Azure aktivitets logg poster som är relaterade till Azure SQL Database instanser.

Förstå Avancerat skydd och aviseringar för Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Konfigurera anpassade aviseringar för Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: aktivera aviseringar för avvikande aktivitet

**Vägledning**: Använd Azure Security Center Avancerat skydd för Azure SQL Database för övervakning och aviseringar om avvikande aktivitet. Aktivera Azure Defender för SQL för dina SQL-databaser. Azure Defender för SQL innehåller funktioner för Visa och åtgärder för att minska risken för databas sårbarheter och upptäcka avvikande aktiviteter som kan tyda på ett hot mot databasen.

Förstå Avancerat skydd och aviseringar för Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Så här aktiverar du Azure Defender för SQL för Azure SQL Database:

https://docs.microsoft.com/azure/azure-sql/database/azure-defender-for-sql

Hantera aviseringar i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: ej tillämpligt; för Azure SQL Database hanteras lösningen mot skadlig kod av Microsoft på den underliggande plattformen.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: ej tillämpligt; DNS-loggning är inte tillämpligt för Azure SQL Database.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

**Vägledning**: ej tillämpligt; granskning av kommando tolken är inte tillämpligt för Azure SQL Database.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [säkerhets kontroll: identitets-och Access Control](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: Azure Active Directory (Azure AD) har inbyggda roller som måste tilldelas explicit och som kan frågas. Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

Så här hämtar du en katalog roll i Azure AD med PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: Azure Active Directory saknar begreppet standard lösen ord. När du konfigurerar en Azure SQL Database-instans rekommenderar vi att du väljer att integrera autentisering med Azure Active Directory.

Konfigurera och hantera Azure Active Directory-autentisering med Azure SQL:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa principer och procedurer kring användningen av dedikerade administrativa konton. Använd Azure Security Center identitets-och åtkomst hantering för att övervaka antalet administrativa konton.

Förstå Azure Security Center identitet och åtkomst:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: ej tillämpligt; även om du kan konfigurera Azure Active Directory autentisering för att integrera med Azure SQL Database, stöds inte enkel inloggning.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory baserad åtkomst

**Vägledning**: Aktivera Azure Active Directory (Azure AD) Multi-Factor Authentication (MFA) och följ rekommendationer för Azure Security Center identitets-och åtkomst hantering.

Så här aktiverar du MFA i Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Övervaka identitet och åtkomst i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd en privilegie rad åtkomst arbets Station (Paw) med Multi-Factor Authentication MFA konfigurerat för att logga in på och konfigurera Azure-resurser.

Lär dig mer om arbets stationer med privilegie rad åtkomst:

https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

Så här aktiverar du MFA i Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkt aktivitet från administrativa konton

**Vägledning**: Använd Azure Active Directory säkerhets rapporter för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön.

Använd avancerat skydd för Azure SQL Database för att identifiera avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Så här övervakar du användarnas identitets-och åtkomst aktiviteter i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

Granska Avancerat skydd och potentiella aviseringar:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: använda villkorlig åtkomst med namngivna platser för att tillåta åtkomst till Portal-och Azure-resurser enbart för vissa logiska grupperingar av IP-adressintervall eller länder/regioner.

Så här konfigurerar du namngivna platser i Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: skapa en Azure Active Directory-administratör (Azure AD) för servern.

Konfigurera och hantera Azure Active Directory-autentisering med Azure SQL:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

Så här skapar och konfigurerar du en Azure AD-instans:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure Active Directory (Azure AD) innehåller loggar som hjälper till att identifiera inaktuella konton. Dessutom kan du använda Azure Identity Access-granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användarnas åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

Så här använder du granskningar av Azure Identity Access:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: övervaka försök att komma åt inaktiverade konton

**Vägledning**: Konfigurera Azure Active Directory (Azure AD)-autentisering med Azure SQL och skapa diagnostikinställningar för Azure Active Directory användar konton, skicka gransknings loggar och inloggnings loggar till en Log Analytics-arbetsyta. Konfigurera önskade aviseringar i Log Analytics arbets ytan.

Konfigurera och hantera Azure Active Directory-autentisering med Azure SQL:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

Så här integrerar du Azure-aktivitets loggar i Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: avisering om beteende för beteende för konto inloggning

**Vägledning**: Använd Azure Active Directory (Azure AD) identitets skydd och risk identifieringar för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter. Dessutom kan du mata in data i Azure Sentinel för ytterligare undersökning.

Så här visar du Azure AD-riskfyllda inloggningar:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Så här konfigurerar och aktiverar du principer för identitets skydds risker:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: i support scenarier där Microsoft behöver åtkomst till kund information tillhandahåller Azure Customer lockbox ett gränssnitt som kunder kan använda för att granska och godkänna eller avvisa förfrågningar om kund data åtkomst.

Förstå Customer Lockbox:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [säkerhets kontroll: data skydd](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information.

Skapa och använda Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera separata prenumerationer och/eller hanterings grupper för utveckling, testning och produktion. Resurser bör åtskiljas av VNet/undernät, taggas på lämpligt sätt och skyddas inom en NSG eller Azure-brandvägg. Resurser som lagrar eller bearbetar känsliga data bör isoleras. Använd privat länk; Distribuera Azure SQL Database i ditt VNet och Anslut privat med privata slut punkter.

Så här skapar du ytterligare Azure-prenumerationer:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Så här skapar du Hanteringsgrupper:

https://docs.microsoft.com/azure/governance/management-groups/create

Skapa och använda Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Så här konfigurerar du en privat länk för Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: för databaser i Azure SQL Database lagring eller bearbetning av känslig information markerar du databasen och relaterade resurser som känsliga med taggar. Konfigurera privat länk tillsammans med tjänst taggar för nätverks säkerhets grupper på dina Azure SQL Database instanser för att förhindra exfiltrering av känslig information.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

Så här konfigurerar du privat länk-och NSG: er för att förhindra data exfiltrering på dina Azure SQL Database-instanser:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview

Förstå kundens data skydd i Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: Azure SQL Database skyddar dina data genom att kryptera data i rörelse med Transport Layer Security. SQL Database använder kryptering (SSL/TLS) hela tiden för alla anslutningar. Detta säkerställer att alla data krypteras "under överföring" mellan klienten och servern oavsett inställningen för kryptering eller TrustServerCertificate i anslutnings strängen.

Förstå Azure SQL-kryptering under överföring:

https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: använd funktionen Azure SQL Database data identifiering och klassificering. Data identifiering och klassificering innehåller avancerade funktioner som är inbyggda i Azure SQL Database för att upptäcka, klassificera och märka, &amp; skydda känsliga data i dina databaser.

Så här använder du data identifiering och klassificering för Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser

**Vägledning**: använda Azure Active Directory (Azure AD) för att autentisera och kontrol lera åtkomst till Azure SQL Database instanser.

Så här integrerar du Azure SQL Database med Azure Active Directory för autentisering:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication

Så här kontrollerar du åtkomst i Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-control-access

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: Microsoft hanterar den underliggande infrastrukturen för Azure SQL Database och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information.

Förstå kundens data skydd i Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: transparent data kryptering (TDE) hjälper till att skydda Azure SQL Database, Azure SQL-hanterad instans och Azure Data Warehouse mot hot från skadlig offline-aktivitet genom att kryptera data i vila. TDE utför realtidskryptering och realtidsdekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler i vila, utan att några ändringar krävs i programmet. Som standard är TDE aktiverat för alla nyligen distribuerade databaser i SQL Database och SQL-hanterad instans. Krypterings nyckeln för TDE kan hanteras av antingen Microsoft eller kunden.

Hantera transparent data kryptering och Använd dina egna krypterings nycklar:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i produktions instanser av Azure SQL Database och andra kritiska eller relaterade resurser.

Så här skapar du aviseringar för Azure aktivitets logg händelser:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [säkerhets kontroll: sårbarhets hantering](../../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: Aktivera Azure Defender för SQL för Azure SQL Database och följ rekommendationer från Azure Security Center om att utföra sårbarhets bedömningar på dina servrar.

Så här kör du sårbarhets bedömningar på Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment

Så här aktiverar du Azure Defender för SQL:

https://docs.microsoft.com/azure/azure-sql/database/azure-defender-for-sql

Så här implementerar du rekommendationer för Azure Security Center sårbarhets bedömning:

https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Distribuera automatisk hanterings lösning för program uppdatering från tredje part

**Vägledning**: ej tillämpligt; Detta riktmärke är avsett för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: Aktivera regelbundna återkommande genomsökningar för dina Azure SQL Database instanser; Detta konfigurerar en sårbarhets bedömning för att automatiskt köra en genomsökning i databasen en gång per vecka. En sammanfattning av skannings resultatet skickas till e-postadresserna som du anger. Jämför resultaten för att verifiera att sårbarheter har åtgärd ATS.

Så här exporterar du en sårbarhets utvärderings rapport i Azure Security Center:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment#implementing-vulnerability-assessment

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: Använd standard risk klassificeringarna (säkra poäng) som tillhandahålls av Azure Security Center.

Förstå Azure Security Center säkra Poäng:

https://docs.microsoft.com/azure/security-center/security-center-secure-score

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [säkerhets kontroll: inventering och till gångs hantering](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Använd Azure Asset Discovery

**Vägledning**: Använd Azure Resource Graph för att fråga och identifiera alla resurser (inklusive Azure SQL Database) i dina prenumerationer.  Se till att du har rätt (Läs) behörigheter i din klient och kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via resurs diagram, rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser som går framåt.

Så här skapar du frågor med Azure Resource Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Så här visar du dina Azure-prenumerationer: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Förstå Azure RBAC: https://docs.microsoft.com/azure/role-based-access-control/overview

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser som ger metadata till att logiskt organisera dem i en taxonomi.

Skapa och använda Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer, vid behov, för att organisera och spåra till gångar. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

Så här skapar du ytterligare Azure-prenumerationer:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Så här skapar du Hanteringsgrupper:

https://docs.microsoft.com/azure/governance/management-groups/create

Skapa och använda Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: underhåll en inventering av godkända Azure-resurser och program varu titlar

**Vägledning**: definiera listan över godkända Azure-resurser och godkänd program vara för dina beräknings resurser

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Använd Azure Resource Graph för att fråga/identifiera resurser i dina prenumerationer. Se till att alla Azure-resurser som finns i miljön är godkända.

Så här konfigurerar och hanterar du Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här skapar du frågor med Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Övervakning i Azure Security Center**: Ej tillämpligt

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

Så här konfigurerar och hanterar du Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här nekar du en speciell resurs typ med Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="610-implement-approved-application-list"></a>6,10: implementera lista över godkända program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för program som körs på beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: begränsa användarnas möjlighet att interagera med Azures resurs hanterare via skript

**Vägledning**: Använd villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för App Service-eller beräknings resurser som är värdar för skriv bord eller webb program.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [säkerhets kontroll: säker konfiguration](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: Använd Azure Policy eller Azure Security Center rekommendationer för Azure SQL Database för att underhålla säkerhetskonfigurationer för alla Azure-resurser.

Så här konfigurerar och hanterar du Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar i dina Azure-resurser.

Så här konfigurerar och hanterar du Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Förstå Azure Policys effekter:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: om du använder anpassade Azure policys definitioner använder du Azure DevOps eller Azure databaser för att lagra och hantera din kod på ett säkert sätt.

Så här lagrar du kod i Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentation om Azure databaser:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: Distribuera hanterings verktyg för system konfiguration

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. SQL" för att skapa anpassade principer för avisering, granskning och system konfiguration. Dessutom kan du utveckla en process och pipeline för att hantera princip undantag.

Så här konfigurerar och hanterar du Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: distribuera system konfigurations hanterings verktyg för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementera automatisk konfigurations övervakning för Azure-tjänster

**Vägledning**: utnyttja Azure Security Center för att utföra bas linje genomsökningar för Azure SQL Database.

Så här åtgärdar du rekommendationer i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Använd Azure Key Vault för att lagra krypterings nycklar för Azure SQL Database transparent DATAKRYPTERING (TDE).

Skydda känsliga data som lagras i Azure SQL Database och lagra krypterings nycklarna i Azure Key Vault:

https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Med hanterade identiteter kan du autentisera till vilken tjänst som helst som stöder Azure AD-autentisering, inklusive Azure Key Vault utan autentiseringsuppgifter i din kod.

Självstudie: Använd en Windows VM-systemtilldelad hanterad identitet för att få åtkomst till Azure SQL:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql

Så här konfigurerar du hanterade identiteter:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifter för inloggning för att identifiera autentiseringsuppgifter i din kod. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault.

Konfigurera inloggnings skannern: https://secdevtools.azurewebsites.net/helpcredscan.html

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [säkerhets kontroll: försvar mot skadlig kod](../../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Använd centralt hanterat program mot skadlig kod

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser. Microsoft hanterar skydd mot skadlig kod för den underliggande plattformen.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som har stöd för Azure-tjänster (till exempel Azure App Service), men det körs inte på kund innehåll.

Förskanna allt innehåll som laddas upp till Azure-resurser som inte är Compute, till exempel App Service, Data Lake Storage, Blob Storage, Azure SQL Database osv. Microsoft kan inte komma åt dina data i dessa instanser.

Förstå Microsoft Antimalware för Azure Cloud Services och Virtual Machines: https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för skadlig program vara uppdateras

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser. Microsoft hanterar skydd mot skadlig kod för den underliggande plattformen.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [säkerhets kontroll: Data återställning](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: se till att vanlig automatisk säkerhets kopiering UPS

**Vägledning**: för att skydda din verksamhet mot data förlust skapar Azure SQL Database automatiskt fullständiga säkerhets kopieringar varje vecka, differentiella databas säkerhets kopior var 12: e timme och säkerhets kopieringar av transaktions loggar var 5-10 minut. Säkerhets kopiorna lagras i RA-GRS-lagring under minst 7 dagar för alla tjänst nivåer. Alla tjänst nivåer utom Basic Support konfigurerbar kvarhållning av säkerhets kopior för återställning till tidpunkt, upp till 35 dagar.

För att uppfylla olika krav på efterlevnad kan du välja olika kvarhållningsperioden för säkerhets kopiering varje vecka, månatlig och/eller år. Lagrings förbrukningen beror på den valda frekvensen av säkerhets kopior och kvarhållningsperioden.

Förstå säkerhets kopiering och affärs kontinuitet med Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-business-continuity

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: Azure SQL Database skapar automatiskt de databas säkerhets kopior som hålls mellan 7 och 35 dagar och använder Azure Read-Access Geo-redundant lagring (RA-GRS) för att säkerställa att de bevaras även om data centret inte är tillgängligt. Dessa säkerhets kopior skapas automatiskt. Om det behövs aktiverar du långsiktiga geo-redundanta säkerhets kopieringar för dina Azure SQL-databaser.

Om du använder Kundhanterade nycklar för transparent datakryptering, se till att dina nycklar säkerhets kopie ras.

Förstå säkerhets kopieringar i Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database

Så här säkerhetskopierar du nyckel valv nycklar i Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: Säkerställ att du regelbundet kan utföra Data återställning av innehåll inom Azure Backup. Om det behövs kan du testa att återställa innehåll till ett isolerat VLAN. Testa återställning av säkerhetskopierade nycklar som hanteras av kunden.

Så här återställer du Key Vault-nycklar i Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

Återställa Azure SQL Database säkerhets kopior med hjälp av återställning av tidpunkt:

https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: Aktivera mjuk borttagning i Azure Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.

Så här aktiverar du mjuk borttagning i Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [säkerhets kontroll: incident svar](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: kontrol lera att det finns skriftliga svars planer för incidenter som definierar personal roller och faser för incident hantering/hantering.

Konfigurera automatisering av arbets flöden i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till aviseringar, som hjälper dig att prioritera i vilken ordning du deltar i varje avisering, så att när en resurs komprometteras kan du komma åt den direkt. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Säkerhets aviseringar i Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems incident svars funktioner på en vanlig takt. Identifiera svaga punkter och luckor, och ändra planen efter behov.

Du kan läsa NIST: guide för att testa, träna och träna program för IT-planer och funktioner:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en olagligt eller obehörig part.

Så här ställer du in Azure Security Center säkerhets kontakt:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviseringarna till kontroll.

Så här konfigurerar du kontinuerlig export:

https://docs.microsoft.com/azure/security-center/continuous-export

Strömma aviseringar till Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöden i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer.

Konfigurera automatisering av arbets flöde och Logic Apps:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [säkerhets kontroll: inträngande tester och röda team övningar](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att alla kritiska säkerhets brister upptäcks inom 60 dagar

**Vägledning**: Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Du hittar mer information om Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft Managed Cloud Infrastructure, tjänster och program, här: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../../security/benchmarks/overview.md)
- Läs mer om [Azures säkerhets bas linjer](../../security/benchmarks/security-baselines-overview.md)