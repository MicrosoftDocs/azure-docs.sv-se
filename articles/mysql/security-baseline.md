---
title: Azures säkerhets bas linje för Azure Database for MySQL
description: Azure Database for MySQL säkerhets bas linje ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: mysql
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 62501d6174b9ef8806de0bbdae0dfedfe70da03a
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968092"
---
# <a name="azure-security-baseline-for-azure-database-for-mysql"></a>Azures säkerhets bas linje för Azure Database for MySQL

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 1,0](../security/benchmarks/overview-v1.md) till Azure Database for MySQL. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure. Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för Azure Database for MySQL. **Kontroller** som inte är tillämpliga på Azure Database for MySQL, eller för vilka ansvaret är Microsofts, har uteslutits.

Om du vill se hur Azure Database for MySQL helt mappar till Azures säkerhets mätning, se den [fullständiga Azure Database for MySQL mappnings filen för säkerhets bas linjen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: Konfigurera en privat länk för Azure Database for MySQL med privata slut punkter. Med Private Link kan du ansluta till olika PaaS-tjänster i Azure via en privat slutpunkt. Azure Private Link ger dig tillgång till Azure-tjänster i ditt privata virtuella nätverk (VNet). Trafiken mellan det virtuella nätverket och MySQL-instansen skickas till Microsoft stamnätet-nätverket.

Du kan också använda Virtual Network tjänstens slut punkter för att skydda och begränsa nätverks åtkomsten till dina Azure Database for MySQL-implementeringar. Regler för virtuella nätverk är en brand Väggs säkerhetsfunktion som styr om din Azure Database for MySQL server accepterar kommunikation som skickas från vissa undernät i virtuella nätverk.

Du kan också skydda Azure Database for MySQL-servern med brand Väggs regler. Server brand väggen förhindrar all åtkomst till din databas server tills du anger vilka datorer som har behörighet. Du konfigurerar brandväggen genom att skapa brandväggsregler som anger intervall med godkända IP-adresser. Du kan skapa brand Väggs regler på server nivå.

- [Konfigurera en privat länk för Azure Database for MySQL](howto-configure-privatelink-portal.md)

- [Skapa och hantera VNet-tjänstens slut punkter och VNet-regler i Azure Database for MySQL](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)

- [Så här konfigurerar du Azure Database for MySQL brand Väggs regler](howto-manage-firewall-using-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: [Azures säkerhets benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standard policy initiativ för Security Center och är grunden för [Security Center rekommendationer](/azure/security-center/security-center-recommendations). De Azure Policy-definitioner som är relaterade till den här kontrollen aktive ras automatiskt av Security Center. Aviseringar som är relaterade till den här kontrollen kan kräva en [Azure Defender](/azure/security-center/azure-defender) -plan för de relaterade tjänsterna.

**Azure policy inbyggda definitioner – Microsoft. DBforMySQL**:

[!INCLUDE [Resource Policy for Microsoft.DBforMySQL 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.dbformysql-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverks gränssnitt

**Vägledning**: när din Azure Database for MySQL-instans är skyddad till en privat slut punkt kan du distribuera virtuella datorer i samma virtuella nätverk. Du kan använda en nätverks säkerhets grupp (NSG) för att minska risken för data exfiltrering. Aktivera NSG Flow-loggar och skicka loggar till ett lagrings konto för trafik granskning. Du kan också skicka NSG Flow-loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflöde i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

- [Konfigurera en privat länk för Azure Database for MySQL](howto-configure-privatelink-portal.md)

- [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Använd avancerat skydd för Azure Database for MySQL. Avancerat skydd identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

Aktivera DDoS Protection standard på de virtuella nätverk som är kopplade till dina Azure Database for MySQL instanser för att skydda mot DDoS-attacker. Använd Azure Security Center integrerad Hot information för att neka kommunikation med kända skadliga eller oanvända Internet-IP-adresser.

- [Så här konfigurerar du Avancerat skydd för Azure Database for MySQL](howto-database-threat-protection-portal.md)

- [Så här konfigurerar du DDoS-skydd](../ddos-protection/manage-ddos-protection.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="15-record-network-packets"></a>1,5: registrera nätverks paket

**Vägledning**: när din Azure Database for MySQL-instans är skyddad till en privat slut punkt kan du distribuera virtuella datorer i samma virtuella nätverk. Du kan sedan konfigurera en nätverks säkerhets grupp (NSG) för att minska risken för data exfiltrering. Aktivera NSG Flow-loggar och skicka loggar till ett lagrings konto för trafik granskning. Du kan också skicka NSG Flow-loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflöde i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

- [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera nätverksbaserade intrångs identifiering/system för skydd mot intrång (ID/IP-adresser)

**Vägledning**: Använd avancerat skydd för Azure Database for MySQL. Avancerat skydd identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

- [Så här konfigurerar du Avancerat skydd för Azure Database for MySQL](howto-database-threat-protection-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: för resurser som behöver åtkomst till dina Azure Database for MySQL-instanser använder du Virtual Network Service-taggar för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper eller Azure-brandvägg. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange namnet på service tag gen (t. ex. SQL. Väst) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

Obs: Azure Database for MySQL använder service taggarna Microsoft. SQL.

- [Mer information om att använda service märken](../virtual-network/service-tags-overview.md)

- [Förstå användningen av service tag i Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet#terminology-and-description)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för nätverks inställningar och nätverks resurser som är kopplade till dina Azure Database for MySQL-instanser med Azure policy. Använd Azure Policy alias i namn områdena "Microsoft. DBforMySQL" och "Microsoft. Network" om du vill skapa anpassade principer för granskning eller framtvinga nätverks konfigurationen för dina Azure Database for MySQLs instanser. Du kan också använda inbyggda princip definitioner som är relaterade till nätverk eller Azure Database for MySQL-instanser, till exempel:

- DDoS Protection standard ska vara aktive rad

- Tvinga SSL-anslutning ska vara aktive rad för MySQL-databas servrar

Läs mer i följande referenser:

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy exempel för nätverk](/azure/governance/policy/samples/)

- [Så här skapar du en Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för resurser som rör nätverks säkerhets-och trafikflödet för dina Azure Database for MySQL-instanser för att tillhandahålla metadata och logisk organisation.

Använd någon av de inbyggda Azure Policy definitionerna som är relaterade till taggning, t. ex. **Kräv tagg och dess värde** för att säkerställa att alla resurser skapas med taggar och meddela dig om befintliga otaggade resurser.

Du kan använda Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar för nätverks resurser som är relaterade till dina Azure Database for MySQL-instanser. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks resurser sker.

- [Visa och hämta Azure aktivitets logg händelser](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets benchmark för Azure: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: Aktivera diagnostikinställningar och Server loggar och mata in loggar för att samla in säkerhets data som genereras av Azure Database for MySQL instanser. I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring. Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM från tredje part.

- [Förstå Server loggar för Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Aktivera diagnostikinställningar på Azure Database for MySQL instanser för åtkomst till gransknings-, långsam fråga-och MySQL-statistik loggar. Se till att du specifikt aktiverar MySQL-gransknings loggen. Aktivitets loggar, som är automatiskt tillgängliga, innehåller händelse källa, datum, användare, tidsstämpel, käll adresser, mål adresser och andra användbara element. Du kan också aktivera inställningar för Azure aktivitets loggs diagnostik och skicka loggarna till samma Log Analytics-arbetsyta eller lagrings konto.

- [Förstå Server loggar för Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs)

- [Så här konfigurerar och kommer du åt långsamma Query-loggar för Azure Database for MySQL](howto-configure-server-logs-in-portal.md)

- [Konfigurera och få åtkomst till gransknings loggar för Azure Database for MySQL](howto-configure-audit-logs-portal.md)

- [Konfigurera diagnostikinställningar för Azure aktivitets logg](../azure-monitor/essentials/activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: i Azure Monitor för arbets ytan Log Analytics som används för att hålla dina Azure Database for MySQL loggar, anger du kvarhållningsperioden enligt organisationens regler för efterlevnad. Använd Azure Storage konton för långsiktig/Arkiv lagring.

- [Ange logg lagrings parametrar för Log Analytics arbets ytor](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Lagra resurs loggar i ett Azure Storage konto](https://docs.microsoft.com/azure/azure-monitor/essentials/resource-logs#send-to-azure-storage)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: analysera och övervaka loggar från Azure Database for MySQL instanser för avvikande beteende. Använd Azure Monitor Log Analytics för att granska loggar och köra frågor om loggdata. Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM från tredje part.

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Mer information om Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Så här utför du anpassade frågor i Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: Aktivera avancerat skydd för Azure Database for MySQL. Avancerat skydd identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

Dessutom kan du aktivera server loggar och diagnostikinställningar för MySQL och skicka loggar till en Log Analytics-arbetsyta. Publicera din Log Analytics-arbetsyta till Azure-kontroll när den tillhandahåller en SOAR-lösning (Security Orchestration autoresponse). Detta gör det möjligt för spel böcker (automatiserade lösningar) att skapas och användas för att åtgärda säkerhets problem.

- [Så här aktiverar du Avancerat skydd för Azure Database for MySQL (för hands version)](howto-database-threat-protection-portal.md)

- [Förstå Server loggar för Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs)

- [Så här konfigurerar och kommer du åt långsamma Query-loggar för Azure Database for MySQL](howto-configure-server-logs-in-portal.md)

- [Konfigurera och få åtkomst till gransknings loggar för Azure Database for MySQL](howto-configure-audit-logs-portal.md)

- [Konfigurera diagnostikinställningar för Azure aktivitets logg](../azure-monitor/essentials/activity-log.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security benchmark: identitet och Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: underhålla en inventering av de användar konton som har administrativ åtkomst till hanterings planet (t. ex. Azure Portal) för Azure Database för MySQLinstances. Dessutom bör du underhålla en inventering av de administrativa konton som har åtkomst till data planet (i själva databasen) av dina Azure Database for MySQL instanser. (När du skapar MySQL-servern anger du autentiseringsuppgifter för en administratörs användare. Den här administratören kan användas för att skapa ytterligare MySQL-användare.)

Azure Database for MySQL stöder inte inbyggd rollbaserad åtkomst kontroll, men du kan skapa anpassade roller baserat på vissa resurs leverantörs alternativ.

- [Förstå anpassade roller för Azure-prenumerationen](../role-based-access-control/custom-roles.md) 

- [Förstå Azure Database for MySQL Resource Provider-åtgärder](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbformysql)

- [Förstå åtkomst hantering för Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/concepts-security#access-management)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: Azure Active Directory (Azure AD) har inte begreppet standard lösen ord.

När du skapar själva Azure Database for MySQL resursen tvingar Azure dig att skapa en administrativ användare med ett starkt lösen ord. När MySQL-instansen har skapats kan du dock använda det första server administratörs konto som du skapade för att skapa ytterligare användare och ge administrativ åtkomst till dem. När du skapar dessa konton bör du se till att konfigurera ett annat, starkt lösen ord för varje konto.

- [Så här skapar du ytterligare konton för Azure Database for MySQL](howto-create-users.md)

- [Så här uppdaterar du administratörs lösen ord](https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton som har åtkomst till dina Azure Database for MySQL-instanser. Använd Azure Security Center identitets-och åtkomst hantering för att övervaka antalet administrativa konton.

- [Förstå Azure Security Center identitet och åtkomst](../security-center/security-center-identity-access.md)

- [Lär dig hur du skapar administratörs användare i Azure Database for MySQL](howto-create-users.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Använd Azure Active Directory enkel inloggning (SSO)

**Vägledning**: vid inloggning på Azure Database for MySQL stöds både användar namn och lösen ord som kon figurer ATS direkt i databasen, samt användning av en Azure Active Directory (Azure AD)-identitet och användning av en Azure AD-token för anslutning. När du använder en Azure AD-token stöds olika metoder, till exempel en Azure AD-användare, en Azure AD-grupp eller ett Azure AD-program som ansluter till databasen.

Kontroll Plans åtkomst för MySQL är separat tillgänglig via REST API och stöder SSO. Du autentiserar genom att ange Authorization-huvudet för dina begär anden till ett JSON Web Token som du hämtar från Azure AD.

- [Använda Azure AD för autentisering med Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md)

- [Förstå Azure Database for MySQL REST API](/rest/api/mysql/)

- [Förstå SSO med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory-baserad åtkomst

**Vägledning**: Aktivera Azure Active Directory (Azure AD) multifaktorautentisering och Azure Security Center Följ rekommendationerna för identitets-och åtkomst hantering. När du använder Azure AD-token för att logga in i databasen kan du kräva multifaktorautentisering för databas inloggningar.

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Använda Azure AD för autentisering med Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md)

- [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Använd säkra, Azure-hanterade arbets stationer för administrativa uppgifter

**Vägledning**: Använd Privileged Access-arbetsstationer (Paw) med multifaktorautentisering konfigurerad för att logga in på och konfigurera Azure-resurser.

- [Lär dig mer om arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: Aktivera avancerat skydd för Azure Database for MySQL om du vill generera aviseringar för misstänkt aktivitet.

Dessutom kan du använda Azure Active Directory (Azure AD) Privileged Identity Management (PIM) för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön.

Använd identifieringar av Azure AD-risker för att visa aviseringar och rapporter om riskfyllda användar beteenden.

- [Så här konfigurerar du Avancerat skydd för Azure Database for MySQL](howto-database-threat-protection-portal.md)

- [Distribuera Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Förstå identifieringar av Azure AD-risker](../active-directory/identity-protection/overview-identity-protection.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: använda villkorlig åtkomst med namngivna platser för att tillåta portal och Azure Resource Manager åtkomst från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner.

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: använda Azure Active Directory (Azure AD) som central autentiserings-och auktoriserings system. Azure AD skyddar data med stark kryptering för data i vila och under överföring. Azure AD innehåller även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

För att logga in på Azure Database for MySQL rekommenderar vi att du använder Azure AD och använder en Azure AD-token för att ansluta. När du använder en Azure AD-token stöds olika metoder, till exempel en Azure AD-användare, en Azure AD-grupp eller ett Azure AD-program som ansluter till databasen.

Autentiseringsuppgifter för Azure AD kan också användas för administration på hanterings planet nivå (t. ex. Azure Portal) för att kontrol lera MySQL-administratörskonton.

- [Använda Azure AD för autentisering med Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: granska Azure Active Directory (Azure AD)-loggarna för att identifiera inaktuella konton som kan inkludera dem med Azure Database for MySQL administrativa roller. Använd dessutom Azure Identity Access-granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program som kan användas för att få åtkomst till Azure Database for MySQL och roll tilldelningar. Användar åtkomsten bör granskas regelbundet, till exempel var 90: e dag och se till att endast rätt användare har fortsatt åtkomst.

- [Förstå Azure AD repor ting](/azure/active-directory/reports-monitoring/)

- [Så här använder du granskningar av Azure Identity Access](../active-directory/governance/access-reviews-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: Aktivera diagnostikinställningar för Azure Database for MySQL och Azure Active Directory (Azure AD) och skicka alla loggar till en Log Analytics-arbetsyta. Konfigurera önskade aviseringar (t. ex. misslyckade autentiseringsförsök) i Log Analytics.

- [Så här konfigurerar och kommer du åt långsamma Query-loggar för Azure Database for MySQL](/Azure/mysql/howto-configure-server-logs-in-portal)

- [Konfigurera och få åtkomst till gransknings loggar för Azure Database for MySQL](/Azure/mysql/howto-configure-audit-logs-portal)

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: varning vid inloggnings beteende för konto

**Vägledning**: Aktivera avancerat skydd för Azure Database for MySQL om du vill generera aviseringar för misstänkt aktivitet.

Använd Azure Active Directory (Azure AD) för identitets skydd och identifiering av risker för att konfigurera automatiserade svar på identifierade misstänkta åtgärder. Du kan aktivera automatiserade svar via Azure Sentinel för att implementera din organisations säkerhets svar.

Du kan också mata in loggar i Azure Sentinel för ytterligare undersökning.

- [Så här konfigurerar du Avancerat skydd för Azure Database for MySQL](howto-database-threat-protection-portal.md)

- [Översikt över Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra Azure Database for MySQL instanser eller relaterade resurser som lagrar eller bearbetar känslig information.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera separata prenumerationer och/eller hanterings grupper för utveckling, testning och produktion. Använd en kombination av privata länkar, tjänst slut punkter och/eller brand Väggs regler för att isolera och begränsa nätverks åtkomsten till dina Azure Database for MySQL-instanser.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du hanterings grupper](../governance/management-groups/create-management-group-portal.md)

- [Konfigurera en privat länk för Azure Database for MySQL](concepts-data-access-security-private-link.md)

- [Skapa och hantera VNet-tjänstens slut punkter och VNet-regler i Azure Database for MySQL](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)

- [Så här konfigurerar du Azure Database for MySQL brand Väggs regler](concepts-firewall-rules.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: när du använder virtuella Azure-datorer för att komma åt Azure Database for MySQL instanser, använder du privata länkar, MySQL-nätverkskonfigurationer, nätverks säkerhets grupper och service märken för att minimera risken för data exfiltrering.

Microsoft hanterar den underliggande infrastrukturen för Azure Database for MySQL och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information.

- [Hur du minimerar data exfiltrering för Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link#data-exfiltration-prevention)

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: Azure Database for MySQL stöder anslutning av MySQL-servern till klient program med hjälp av Secure SOCKETS Layer (SSL). Framtvingande av SSL-anslutningar mellan databasservern och klientprogrammen hjälper till att skydda mot ”man in the middle”-attacker genom att kryptera dataströmmen mellan servern och programmet. Se till att "tvinga SSL-anslutning" är aktiverat för alla Azure Database for MySQL-instanser som standard i Azure Portal.

De TLS-versioner som stöds för Azure Database for MySQL är TLS 1,0, TLS 1,1, TLS 1,2.

- [Konfigurera kryptering i överföring för Azure Database for MySQL](concepts-ssl-connection-security.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: [Azures säkerhets benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standard policy initiativ för Security Center och är grunden för [Security Center rekommendationer](/azure/security-center/security-center-recommendations). De Azure Policy-definitioner som är relaterade till den här kontrollen aktive ras automatiskt av Security Center. Aviseringar som är relaterade till den här kontrollen kan kräva en [Azure Defender](/azure/security-center/azure-defender) -plan för de relaterade tjänsterna.

**Azure policy inbyggda definitioner – Microsoft. DBforMySQL**:

[!INCLUDE [Resource Policy for Microsoft.DBforMySQL 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.dbformysql-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure Database for MySQL. Implementera en lösning från tredje part om det krävs för efterlevnad.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser 

**Vägledning**: Använd rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att kontrol lera åtkomsten till Azure Database for MySQL kontroll planet (t. ex. Azure Portal). För data Plans åtkomst (i själva databasen) använder du SQL-frågor för att skapa användare och konfigurera användar behörigheter. Azure RBAC påverkar inte användar behörigheter i databasen.

- [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Så här konfigurerar du användar åtkomst med SQL för Azure Database for MySQL](howto-create-users.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i produktions instanser av Azure Database for MySQL och andra kritiska eller relaterade resurser.

- [Så här skapar du aviseringar för Azure aktivitets logg händelser](../azure-monitor/alerts/alerts-activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security benchmark: sårbarhet Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: Följ rekommendationer från Azure Security Center om hur du skyddar dina Azure Database for MySQL och relaterade resurser.

Microsoft utför sårbarhets hantering på de underliggande system som har stöd för Azure Database for MySQL.

- [Förstå Azure Security Center rekommendationer](../security-center/recommendations-reference.md)

- [Funktions täckning för Azure PaaS Services i Azure Security Center](../security-center/features-paas.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga och identifiera alla resurser (inklusive Azure Database for MySQL instanser) i dina prenumerationer. Se till att du har rätt (Läs) behörigheter i din klient och kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar för att Azure Database for MySQL instanser och andra relaterade resurser som ger metadata till att logiskt organisera dem i en taxonomi.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer, vid behov, för att ordna och spåra Azure Database for MySQL instanser och relaterade resurser. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du hanterings grupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla inventering av godkända Azure-resurser

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser och Azure som helhet.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Använd dessutom Azure Resource Graph för att fråga efter och identifiera resurser i prenumerationerna.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

Läs mer i följande referenser:

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](/azure/governance/policy/samples/index)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd den villkorliga Azure-åtkomsten för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering". Detta kan förhindra att resurser skapas och ändras i en hög säkerhets miljö, till exempel instanser av Azure Database for MySQL som innehåller känslig information.

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security benchmark: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: definiera och implementera standardkonfigurationer för dina Azure Database for MySQL-instanser med Azure policy. Använd Azure Policy alias i namn området **Microsoft. DBforMySQL** för att skapa anpassade principer för att granska eller framtvinga nätverks konfigurationen för dina Azure Database for MySQL-instanser. Du kan också använda inbyggda princip definitioner som är relaterade till dina Azure Database for MySQL-instanser, till exempel:

Tvinga SSL-anslutning ska vara aktive rad för MySQL-databas servrar

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar i dina Azure-resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: om du använder anpassade Azure policys definitioner för dina Azure Database for MySQL-instanser och relaterade resurser använder du Azure-databaser för att lagra och hantera din kod på ett säkert sätt.

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Dokumentation om Azure databaser](/azure/devops/repos/)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. DBforMySQL" om du vill skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Dessutom kan du utveckla en process och pipeline för att hantera princip undantag.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: Använd Azure policy alias i namn området **Microsoft. DBforMySQL** för att skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Använd Azure Policy [audit], [neka] och [distribuera om det inte finns] för att automatiskt tillämpa konfigurationer för dina Azure Database for MySQL-instanser och relaterade resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: för Azure-Virtual Machines eller-webb program som körs på Azure App Service används för att få åtkomst till dina Azure Database for MySQL-instanser använder du hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda Azure Database for MySQL hemlig hantering. Se till att Key Vault mjuk borttagning har Aktiver ATS.

- [Integrera med Azure Managed Identities](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Så här skapar du en Key Vault](../key-vault/general/quick-create-portal.md)

- [Så här ger Key Vault autentisering med en hanterad identitet](../key-vault/general/assign-access-policy-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: Azure Database for MySQL-instansen stöder Azure Active Directory (Azure AD)-autentisering för att komma åt databaser. När du skapar Azure Database for MySQL-instansen anger du autentiseringsuppgifter för en administratörs användare. Den här administratören kan använda för att skapa ytterligare databas användare.

För Azure-Virtual Machines eller-webb program som körs på Azure App Service används för att få åtkomst till dina Azure Database for MySQL-instanser använder du Hanterad tjänstidentitet tillsammans med Azure Key Vault för att lagra och hämta autentiseringsuppgifter för Azure Database for MySQL-instansen. Se till att Key Vault mjuk borttagning har Aktiver ATS.

Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Med hanterade identiteter kan du autentisera till vilken tjänst som helst som stöder Azure AD-autentisering, inklusive Key Vault utan autentiseringsuppgifter i din kod.

- [Konfigurera hanterade identiteter](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Integrera med Azure Managed Identities](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault.

- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security benchmark: skydd mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som har stöd för Azure-tjänster (till exempel Azure Database for MySQL), men det körs inte på kund innehåll.

Förskanna allt innehåll som laddas upp till Azure-resurser som inte är Compute, till exempel App Service, Data Lake Storage, Blob Storage, Azure Database for MySQL osv. Microsoft kan inte komma åt dina data i dessa instanser.

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Azure Security benchmark: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Säkerställ regelbunden automatisk säkerhets kopiering

**Vägledning**: Azure Database for MySQL tar säkerhets kopior av datafilerna och transaktions loggen. Beroende på den maximala lagrings storleken som stöds tar vi antingen fullständig och differentiell säkerhets kopiering (4 TB max lagrings servrar) eller säkerhets kopior av ögonblicks bilder (upp till 16 TB max lagrings servrar). Med dessa säkerhets kopieringar kan du återställa en server till alla tidpunkter inom den konfigurerade kvarhållningsperioden för säkerhets kopior. Standard kvarhållningsperioden för säkerhets kopiering är sju dagar. Du kan också konfigurera det upp till 35 dagar. Alla säkerhetskopior krypteras med AES 256-bitars kryptering.

- [Förstå säkerhets kopieringar för Azure Database for MySQL](concepts-backup.md)

- [Förstå Azure Database for MySQL inledande konfiguration](tutorial-design-database-using-portal.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: [Azures säkerhets benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standard policy initiativ för Security Center och är grunden för [Security Center rekommendationer](/azure/security-center/security-center-recommendations). De Azure Policy-definitioner som är relaterade till den här kontrollen aktive ras automatiskt av Security Center. Aviseringar som är relaterade till den här kontrollen kan kräva en [Azure Defender](/azure/security-center/azure-defender) -plan för de relaterade tjänsterna.

**Azure policy inbyggda definitioner – Microsoft. DBforMySQL**:

[!INCLUDE [Resource Policy for Microsoft.DBforMySQL 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.dbformysql-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: Azure Database for MySQL skapar automatiskt Server säkerhets kopior och lagrar dem antingen lokalt redundant eller Geo-redundant lagring, enligt användarens val. Säkerhetskopieringar kan användas för att återställa servern till en vald tidpunkt. Säkerhetskopiering och återställning är en viktig del i strategin för affärskontinuitet, eftersom de skyddar dina data från oavsiktlig skada eller borttagning. 

Om du använder Azure Key Vault för att lagra autentiseringsuppgifter för dina Azure Database for MySQL-instanser bör du se till att vanliga automatiserade säkerhets kopieringar av nycklar används. 

- [Förstå säkerhets kopieringar för Azure Database for MySQL](howto-restore-server-portal.md) 

- [Säkerhetskopiera Key Vault nycklar](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Ansvar**: Delad

**Azure Security Center övervakning**: [Azures säkerhets benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standard policy initiativ för Security Center och är grunden för [Security Center rekommendationer](/azure/security-center/security-center-recommendations). De Azure Policy-definitioner som är relaterade till den här kontrollen aktive ras automatiskt av Security Center. Aviseringar som är relaterade till den här kontrollen kan kräva en [Azure Defender](/azure/security-center/azure-defender) -plan för de relaterade tjänsterna.

**Azure policy inbyggda definitioner – Microsoft. DBforMySQL**:

[!INCLUDE [Resource Policy for Microsoft.DBforMySQL 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.dbformysql-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: i Azure Database for MySQL skapar en återställning en ny server från den ursprungliga serverns säkerhets kopior. Det finns två typer av återställning tillgängliga: återställning av tidpunkter och geo-återställning. Återställning av tidpunkt är tillgängligt med alternativ för redundans och skapar en ny server i samma region som den ursprungliga servern. Geo-återställning är bara tillgängligt om du har konfigurerat servern för Geo-redundant lagring och du kan återställa servern till en annan region.

Den uppskattade återställnings tiden beror på flera faktorer, till exempel databasens storlek, transaktions loggens storlek, nätverks bandbredden och det totala antalet databaser som återställs i samma region på samma tid. Återställnings tiden är vanligt vis mindre än 12 timmar.

Testa regelbundet återställning av Azure Database for MySQL instanser.

- [Förstå säkerhets kopiering och återställning i Azure Database for MySQL](concepts-backup.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: Azure Database for MySQL tar fullständiga, differentiella och transaktions logg säkerhets kopior. Med dessa säkerhets kopieringar kan du återställa en server till alla tidpunkter inom den konfigurerade kvarhållningsperioden för säkerhets kopior. Standard kvarhållningsperioden för säkerhets kopiering är sju dagar. Du kan också konfigurera det upp till 35 dagar. Alla säkerhetskopior krypteras med AES 256-bitars kryptering. Se till att Key Vault mjuk borttagning har Aktiver ATS.

- [Förstå säkerhets kopiering och återställning i Azure Database for MySQL](concepts-backup.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.

- [Konfigurera automatisering av arbets flöden i Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Kunden kan också utnyttja NISTs hanterings guide för dator säkerhet för att hjälpa till med att skapa egna incident svars planer](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller analysera den som används för att utfärda aviseringen samt den konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Dessutom är det tydligt att markera prenumerationer (t. ex. produktion, icke-Prod) och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems incident svars funktioner på en vanlig takt. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [Se NIST: guide för test, utbildning och övnings program för IT-planer och funktioner](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har öppnats av en olaglig eller obehörig part.  Granska incidenter när du är säker på att problemen är lösta.

- [Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöden i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer.

- [Konfigurera automatisering av arbets flöden och Logic Apps](../security-center/workflow-automation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [övningen för Azure Security benchmark: inträngande tester och röda team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: Följ Microsoft Cloud inträngande test för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft.

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
