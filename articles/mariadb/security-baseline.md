---
title: Azures säkerhets bas linje för Azure Database for MariaDB
description: Azures säkerhets bas linje för Azure Database for MariaDB
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 99c02644649e133a7634418a5edaaf228d52f0d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98201288"
---
# <a name="azure-security-baseline-for-azure-database-for-mariadb"></a>Azures säkerhets bas linje för Azure Database for MariaDB

Azures säkerhets bas linje för Azure Database for MariaDB innehåller rekommendationer som hjälper dig att förbättra säkerhets position för din distribution.

Bas linjen för den här tjänsten hämtas från [Azures prestandatest version 1,0](../security/benchmarks/overview.md), som ger rekommendationer om hur du kan skydda dina moln lösningar i Azure med våra bästa praxis rikt linjer.

Mer information finns i [Översikt över Azure Security-bas linjer](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets kontroll: nätverks säkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: skydda resurser med hjälp av nätverks säkerhets grupper eller Azure-brandvägg på din Virtual Network

**Vägledning**: Konfigurera en privat länk för Azure Database for MariaDB med privata slut punkter. Med Private Link kan du ansluta till olika PaaS-tjänster i Azure via en privat slutpunkt. Azure Private Link ger dig tillgång till Azure-tjänster i ditt privata virtuella nätverk (VNet). Trafiken mellan ditt virtuella nätverk och MariaDB-instansen skickas till Microsoft stamnätet-nätverket.

Du kan också använda Virtual Network tjänstens slut punkter för att skydda och begränsa nätverks åtkomsten till dina Azure Database for MariaDB-implementeringar. Regler för virtuella nätverk är en brand Väggs säkerhetsfunktion som styr om din Azure Database for MariaDB accepterar kommunikation som skickas från vissa undernät i virtuella nätverk.

Du kan också skydda dina Azure Database for MariaDB med brand Väggs regler. Server brand väggen förhindrar all åtkomst till din databas server tills du anger vilka datorer som har behörighet. Du konfigurerar brandväggen genom att skapa brandväggsregler som anger intervall med godkända IP-adresser. Du kan skapa brand Väggs regler på server nivå.

Så här konfigurerar du en privat länk för Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

Skapa och hantera VNet-tjänstens slut punkter och VNet-regler i Azure Database for MariaDB Server: https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

Så här konfigurerar du Azure Database for MariaDB brand Väggs regler: https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverkskort

**Vägledning**: när din Azure Database for MariaDB-Server är skyddad till en privat slut punkt kan du distribuera virtuella datorer i samma virtuella nätverk. Du kan använda en nätverks säkerhets grupp (NSG) för att minska risken för data exfiltrering. Aktivera NSG Flow-loggar och skicka loggar till ett lagrings konto för trafik granskning. Du kan också skicka NSG Flow-loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflöde i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

Så här konfigurerar du en privat länk för Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

Så här aktiverar du NSG Flow-loggar: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal så här aktiverar och använder du trafikanalys: https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Använd avancerat skydd för Azure Database for MariaDB. Avancerat skydd identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

Aktivera DDoS Protection standard på de virtuella nätverk som är kopplade till dina Azure Database for MariaDB instanser för att skydda mot DDoS-attacker. Använd Azure Security Center integrerad Hot information för att neka kommunikation med kända skadliga eller oanvända Internet-IP-adresser.

Så här konfigurerar du Avancerat skydd för Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Så här konfigurerar du DDoS-skydd: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection



**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: registrera nätverks paket och flödes loggar

**Vägledning**: när din Azure Database for MariaDB-Server är skyddad till en privat slut punkt kan du distribuera virtuella datorer i samma virtuella nätverk. Du kan sedan konfigurera en nätverks säkerhets grupp (NSG) för att minska risken för data exfiltrering. Aktivera NSG Flow-loggar och skicka loggar till ett lagrings konto för trafik granskning. Du kan också skicka NSG Flow-loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflöde i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

Så här aktiverar du NSG Flow-loggar: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal så här aktiverar och använder du trafikanalys: https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera Network-baserad intrångs identifiering/intrångs skydd system (ID/IP-adresser)

**Vägledning**: Använd avancerat skydd för Azure Database for MariaDB. Avancerat skydd identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.
Så här konfigurerar du Avancerat skydd för Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: för resurser som behöver åtkomst till dina Azure Database for MariaDB-instanser använder du tjänst taggar för virtuella nätverk för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper eller Azure-brandvägg. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange namnet på service tag gen (t. ex. SQL. Väst) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.
Obs: Azure Database for MariaDB använder service tag gen "Microsoft. SQL".

Mer information om att använda service märken: https://docs.microsoft.com/azure/virtual-network/service-tags-overview förstå användningen av service tag i Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet#terminology-and-description



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för nätverks inställningar och nätverks resurser som är kopplade till dina Azure Database for MariaDB-instanser med Azure policy. Använd Azure Policy alias i namn områdena "Microsoft. DBforMariaDB" och "Microsoft. Network" om du vill skapa anpassade principer för granskning eller framtvinga nätverks konfigurationen för dina Azure Database for MariaDBs instanser. Du kan också använda inbyggda princip definitioner som är relaterade till nätverk eller Azure Database for MariaDB-instanser, till exempel:

- DDoS Protection standard ska vara aktive rad

- Den privata slut punkten måste vara aktive rad för MariaDB-servrar

- MariaDB-servern bör använda en tjänst slut punkt för virtuellt nätverk

Så här konfigurerar och hanterar du Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy exempel för nätverk: https://docs.microsoft.com/azure/governance/policy/samples/

Så här skapar du en Azure Blueprint: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för resurser som rör nätverks säkerhets-och trafikflödet för dina MariaDB-instanser för att tillhandahålla metadata och logisk organisation.

Använd någon av de inbyggda Azure Policy definitionerna som är relaterade till taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och meddela dig om befintliga otaggade resurser.

Du kan använda Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

Skapa och använda Taggar: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar för nätverks resurser som är relaterade till dina Azure Database for MariaDB-instanser. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks resurser sker.
Så här visar och hämtar du Azure aktivitets logg händelser: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view så här skapar du aviseringar i Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets kontroll: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

**Vägledning**: Microsoft hanterar den tids källa som används för Azure-resurser, till exempel Azure Database for MariaDB för tidsstämplar i loggarna.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: Aktivera diagnostikinställningar och Server loggar och mata in loggar för att samla in säkerhets data som genereras av Azure Database for MariaDB instanser. I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring. Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM från tredje part.
Konfigurera och få åtkomst till Server loggar för Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Så här konfigurerar och får du åtkomst till gransknings loggar för Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal så här integrerar du Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard



**Azure Security Center övervakning**: inte tillgänglig

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Aktivera diagnostikinställningar på Azure Database for MariaDB instanser för åtkomst till gransknings-, säkerhets-och diagnostikloggar. Se till att du specifikt aktiverar gransknings loggen för MariaDB. Aktivitets loggar, som är automatiskt tillgängliga, innehåller händelse källa, datum, användare, tidsstämpel, käll adresser, mål adresser och andra användbara element. Du kan också aktivera inställningar för Azure aktivitets loggs diagnostik och skicka loggarna till samma Log Analytics-arbetsyta eller lagrings konto.

Konfigurera och få åtkomst till Server loggar för Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-server-logs Konfigurera och få åtkomst till gransknings loggar för Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal så här konfigurerar du diagnostikinställningar för Azure-aktivitets loggen: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy



**Azure Security Center övervakning**: inte tillgänglig

**Ansvar**: Kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: i Azure Monitor för arbets ytan Log Analytics som används för att hålla dina Azure Database for MariaDB loggar, anger du kvarhållningsperioden enligt organisationens regler för efterlevnad. Använd Azure Storage konton för långsiktig/Arkiv lagring.
Så här ställer du in logg lagrings parametrar för Log Analytics arbets ytor: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period lagra resurs loggar i ett Azure Storage konto: https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: analysera och övervaka loggar från dina MariaDB-instanser för avvikande beteende. Använd Azure Monitor Log Analytics arbets ytan för att granska loggar och köra frågor om loggdata. Alternativt kan du aktivera och fordonsbaserad information till Azure Sentinel eller en SIEM från tredje part.

Publicera Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Mer information om arbets ytan Log Analytics: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Så här utför du anpassade frågor i Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: aktivera aviseringar för avvikande aktivitet

**Vägledning**: Aktivera avancerat skydd för MariaDB. Avancerat skydd för Azure Database for MariaDB identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

Dessutom kan du aktivera server loggar och diagnostiska inställningar för MariaDB och skicka loggar till en Log Analytics-arbetsyta. Publicera din Log Analytics-arbetsyta till Azure-kontroll när den tillhandahåller en SOAR-lösning (Security Orchestration autoresponse). Detta gör det möjligt för spel böcker (automatiserade lösningar) att skapas och användas för att åtgärda säkerhets problem.

Så här aktiverar du Avancerat skydd för MariaDB: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Konfigurera och få åtkomst till Server loggar för MariDB: https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Så här konfigurerar och får du åtkomst till gransknings loggar för MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

Publicera Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: ej tillämpligt; MariaDB bearbetar eller skapar inte relaterade loggar mot skadlig kod.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: ej tillämpligt; MariaDB bearbetar eller skapar inte DNS-relaterade loggar.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

**Vägledning**: ej tillämpligt; Benchmark är avsett för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [säkerhets kontroll: identitets-och Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: upprätthålla en inventering av de användar konton som har administrativ åtkomst till hanterings planet (Azure Portal/Azure Resource Manager) för dina MariaDB-instanser. Dessutom bör du underhålla en inventering av de administrativa konton som har åtkomst till data planet för dina MariaDB-instanser. (När du skapar MariaDB-servern anger du autentiseringsuppgifter för en administratörs användare. Den här administratören kan användas för att skapa ytterligare MariaDB-användare.)

Förstå åtkomst hantering för MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-security#access-management

Förstå inbyggda Azure-roller för Azure-prenumerationer: https://docs.microsoft.com/azure/role-based-access-control/built-in-roles


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: Azure Active Directory saknar begreppet standard lösen ord.

När MariaDB-resursen skapas tvingar Azure skapandet av en administrativ användare med ett starkt lösen ord. När MariaDB-instansen har skapats kan du dock använda det första server administratörs konto som du skapade för att skapa ytterligare användare och ge administrativ åtkomst till dem. När du skapar dessa konton bör du se till att konfigurera ett annat, starkt lösen ord för varje konto.

Så här skapar du ytterligare konton för MariaDB: https://docs.microsoft.com/azure/mariadb/howto-create-users


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton som har åtkomst till dina MariaDB-instanser. Använd Azure Security Center identitets-och åtkomst hantering för att övervaka antalet administrativa konton.

Förstå Azure Security Center identitet och åtkomst: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: data Plans åtkomst till MariaDB styrs av identiteter som lagras i databasen och inte stöder SSO. Kontroll Plans åtkomst för MariaDB är tillgänglig via REST API och stöder SSO. Du autentiserar genom att ange Authorization-huvudet för dina begär anden till ett JSON Web Token som du får från Azure Active Directory.

Förstå Azure Database for MariaDB REST API: https://docs.microsoft.com/rest/api/mariadb/

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory baserad åtkomst

**Vägledning**: Aktivera Azure AD MFA och följ Azure Security Center rekommendationer för identitets-och åtkomst hantering.

Så här aktiverar du MFA i Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Övervaka identitet och åtkomst i Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd Paw (arbets stationer med privilegie rad åtkomst) med MFA konfigurerat för att logga in på och konfigurera Azure-resurser.

Lär dig mer om arbets stationer med privilegie rad åtkomst: https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

Så här aktiverar du MFA i Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkt aktivitet från administrativa konton

**Vägledning**: Aktivera avancerat skydd för MariaDB för att generera aviseringar för misstänkt aktivitet.

Dessutom kan du använda Azure AD Privileged Identity Management (PIM) för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön. Använd identifieringar av Azure AD-risker för att visa aviseringar och rapporter om riskfyllda användar beteenden.

Konfigurera Avancerat skydd för MariaDB: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Så här distribuerar du Privileged Identity Management (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Förstå identifieringar av Azure AD-risker: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Använd villkorlig åtkomst med namngivna platser för att tillåta åtkomst från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner för att begränsa åtkomsten till Azure-resurser som MariaDB.

Så här konfigurerar du namngivna platser i Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: använda Azure Active Directory (AAD) som central autentiserings-och auktoriserings system. AAD skyddar data genom att använda stark kryptering för data i vila och under överföring. AAD är också salter, hashar och lagrar användarautentiseringsuppgifter på ett säkert sätt.

Azure AD-autentisering kan inte användas för direkt åtkomst till MariaDB-dataplanen. Azure AD-autentiseringsuppgifter kan dock användas för administration på hanterings Plans nivån (t. ex. Azure Portal) för att styra MariaDB-administratörskonton.

Så här uppdaterar du administratörs lösen ord för MariaDB: https://docs.microsoft.com/azure/mariadb/howto-create-manage-server-portal#update-admin-password

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: granska Azure Active Directory loggar för att identifiera inaktuella konton som kan innehålla de administrativa rollerna med MariaDB. Använd dessutom Azure Identity Access-granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program som kan användas för att få åtkomst till MariaDB och roll tilldelningar. Användar åtkomsten bör granskas regelbundet, till exempel var 90: e dag och se till att endast rätt användare har fortsatt åtkomst.

Förstå Azure AD-rapportering: https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Så här använder du granskningar av Azure Identity Access: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: övervaka försök att komma åt inaktiverade konton

**Vägledning**: Aktivera diagnostikinställningar för MariaDB och Azure Active Directory och skicka alla loggar till en Log Analytics arbets yta. Konfigurera önskade aviseringar (t. ex. misslyckade autentiseringsförsök) i Log Analytics arbets ytan.

Konfigurera och få åtkomst till Server loggar för MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Så här konfigurerar och får du åtkomst till gransknings loggar för MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

Så här integrerar du Azure-aktivitets loggar i Azure Monitor: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure Security Center övervakning**: inte tillgänglig

**Ansvar**: Kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: avisering om beteende för beteende för konto inloggning

**Vägledning**: Aktivera avancerat skydd för MariaDB. Avancerat skydd för Azure Database for MariaDB identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

Använd Azure Active Directoryens identitets skydds-och risk identifierings funktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder. Du kan aktivera automatiserade svar via Azure Sentinel för att implementera din organisations säkerhets svar.

Så här aktiverar du Avancerat skydd för MariaDB: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Så här konfigurerar och aktiverar du principer för identitets skydds risker: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Så här visar du Azure AD-riskfyllda inloggningar: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Publicera Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center övervakning**: inte tillgänglig

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: ej tillämpligt; Customer Lockbox ännu inte stöd för Azure Database for MariaDB.

Lista över Customer Lockbox tjänster som stöds: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [säkerhets kontroll: data skydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra Azure Database for MariaDB instanser eller relaterade resurser som lagrar eller bearbetar känslig information.

Skapa och använda Taggar: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera separata prenumerationer och/eller hanterings grupper för utveckling, testning och produktion. Använd en kombination av privata länkar, tjänst slut punkter och/eller MariaDB brand Väggs regler för att isolera och begränsa nätverks åtkomsten till dina MariaDB-instanser.

Så här skapar du ytterligare Azure-prenumerationer: https://docs.microsoft.com/azure/billing/billing-create-subscription

Så här skapar du Hanteringsgrupper: https://docs.microsoft.com/azure/governance/management-groups/create

Så här konfigurerar du en privat länk för Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

Konfigurera tjänst slut punkter för Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

Konfigurera brand Väggs regler för Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Azure Security Center övervakning**: inte tillgänglig

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: när du använder virtuella Azure-datorer för att få åtkomst till MariaDB-instanser kan du använda privata länkar, MariaDB-nätverkskonfigurationer, nätverks säkerhets grupper och service märken för att minimera risken för data exfiltrering.

Microsoft hanterar den underliggande infrastrukturen för MariaDB och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information.

Så här minskar du data exfiltrering för Azure Database for MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

Förstå kundens data skydd i Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: Azure Database for MariaDB stöder anslutning av din Azure Database for MariaDB-server till klient program med hjälp av Transport Layer Security (TLS), tidigare kallat Secure SOCKETS Layer (SSL). Att framtvinga TLS-anslutningar mellan din databas server och dina klient program hjälper till att skydda mot "man i de mittersta" angrepp genom att kryptera data strömmen mellan servern och ditt program. Se till att "tvinga SSL-anslutning" är aktiverat för alla MariaDB-instanser i Azure Portal.

Konfigurera kryptering i överföring för MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-ssl

**Azure Security Center övervakning**: inte tillgänglig

**Ansvar**: Delad

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure Database for MariaDB. Implementera en lösning från tredje part om det krävs för efterlevnad.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

Förstå kundens data skydd i Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center övervakning**: inte tillgänglig

**Ansvar**: Delad

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser

**Vägledning**: Använd rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att kontrol lera åtkomsten till Azure-databasen för MariaDB hanterings plan (Azure Portal/Azure Resource Manager). För data Plans åtkomst (i själva databasen) använder du SQL-frågor för att skapa användare och konfigurera användar behörigheter.

Så här konfigurerar du Azure RBAC: https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Så här konfigurerar du användar åtkomst med SQL för MariaDB: https://docs.microsoft.com/azure/mariadb/howto-create-users

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

Microsoft hanterar den underliggande infrastrukturen för MariaDB och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information.

Förstå kundens data skydd i Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: tjänsten Azure Database for MARIADB använder FIPS 140-2-validerade kryptografisk modul för lagrings kryptering av data i vila. Data, inklusive säkerhets kopior, krypteras på disk, med undantag för tillfälliga filer som skapas vid körning av frågor. Tjänsten använder AES 256-bit-chiffer som ingår i Azure Storage-kryptering och nycklarna hanteras av systemet. Lagringskrypteringen är alltid igång och kan inte inaktiveras.

Förstå kryptering i vila för MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-security

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i produktions instanser av Azure Database for MariaDB och andra kritiska eller relaterade resurser.

Så här skapar du aviseringar för Azure aktivitets logg händelser: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [säkerhets kontroll: sårbarhets hantering](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: inte tillgänglig för tillfället. Azure Security Center har ännu inte stöd för sårbarhets bedömning av Azure Database for MariaDB Server.


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Distribuera automatisk hanterings lösning för program uppdatering från tredje part

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: Microsoft utför sårbarhets hantering på de underliggande system som har stöd för Azure Database for MariaDB Server.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [säkerhets kontroll: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Använd Azure Asset Discovery

**Vägledning**: Använd Azure Resource Graph för att fråga och identifiera alla resurser (inklusive Azure Database for MariaDB Server) i dina prenumerationer. Se till att du har rätt (Läs) behörigheter i din klient och kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Så här skapar du frågor med Azure Resource Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Så här visar du dina Azure-prenumerationer: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription

Förstå Azure RBAC: https://docs.microsoft.com/azure/role-based-access-control/overview

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar för att Azure Database for MariaDB Server och andra relaterade resurser som ger metadata till att logiskt organisera dem i en taxonomi.

Skapa och använda Taggar: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer, vid behov, för att ordna och spåra Azure Database for MariaDB Server och relaterade resurser. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

Så här skapar du ytterligare Azure-prenumerationer: https://docs.microsoft.com/azure/billing/billing-create-subscription

Så här skapar du Hanteringsgrupper: https://docs.microsoft.com/azure/governance/management-groups/create

Skapa och använda Taggar: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: underhåll en inventering av godkända Azure-resurser och program varu titlar

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser och Azure som helhet.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Använd dessutom Azure Resource Graph för att fråga/identifiera resurser i prenumerationerna.

Så här konfigurerar och hanterar du Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här skapar du frågor med Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser och Azure som helhet.



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Så här konfigurerar och hanterar du Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här nekar du en speciell resurs typ med Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="610-implement-approved-application-list"></a>6,10: implementera lista över godkända program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="611-divlimit-users-ability-to-interact-with-azure-resources-manager-via-scriptsdiv"></a>6,11: <div>Begränsa användarnas möjlighet att interagera med Azures resurs hanterare via skript</div>

**Vägledning**: Använd den villkorliga Azure-åtkomsten för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering". Detta kan förhindra att resurser skapas och ändras i en hög säkerhets miljö, Azure Database for MariaDB server som innehåller känslig information.

Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [säkerhets kontroll: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: definiera och implementera standardkonfigurationer för dina Azure Database for MariaDB-instanser med Azure policy. Använd Azure Policy alias i namn området "Microsoft. DBforMariaDB" om du vill skapa anpassade principer för granskning eller framtvinga nätverks konfigurationen för dina Azure Database for MariaDB-servrar. Du kan också använda inbyggda princip definitioner som är relaterade till dina Azure Database for MariaDB-servrar, till exempel:

- Geo-redundant säkerhets kopiering måste aktive ras för Azure Database for MariaDB

Så här visar du tillgängliga Azure Policy alias: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias

Så här konfigurerar och hanterar du Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar i dina Azure-resurser.

Så här konfigurerar och hanterar du Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Förstå Azure Policys effekter: https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: om du använder anpassade Azure policys definitioner för dina Azure Database for MariaDB-servrar och relaterade resurser använder du Azure-databaser för att lagra och hantera din kod på ett säkert sätt.

[Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

[Dokumentation om Azure databaser](/azure/devops/repos/index?view=azure-devops&preserve-view=true)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: Distribuera hanterings verktyg för system konfiguration

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. DBforMariaDB" om du vill skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Dessutom kan du utveckla en process och pipeline för att hantera princip undantag.

Så här konfigurerar och hanterar du Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: distribuera system konfigurations hanterings verktyg för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementera automatisk konfigurations övervakning för Azure-tjänster

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. DBforMariaDB" om du vill skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Använd Azure Policy [audit], [neka] och [distribuera om det inte finns] för att automatiskt tillämpa konfigurationer för dina Azure Database for MariaDB-instanser och relaterade resurser.

Så här konfigurerar och hanterar du Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: för Azure-Virtual Machines eller-webb program som körs på Azure App Service används för att få åtkomst till dina Azure Database for MariaDB-servrar, använder du hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda Azure Database for MariaDB hantering av Server hemlighet. Se till att Key Vault mjuk borttagning har Aktiver ATS.

Så här integrerar du med Azure Managed Identities: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Så här skapar du en Key Vault: https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Så här autentiserar du till Key Vault: https://docs.microsoft.com/azure/key-vault/general/authentication

Så här tilldelar du en Key Vault åtkomst princip: https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: Azure Database for MariaDB servern har för närvarande inte stöd för Azure Active Directory-autentisering för att få åtkomst till databaser.  När du skapar Azure Database for MariaDB-servern anger du autentiseringsuppgifter för en administratörs användare. Den här administratören kan användas för att skapa ytterligare MariaDB-användare.  

För Azure-Virtual Machines eller-webb program som körs på Azure App Service används för att få åtkomst till din Azure Database for MariaDB-Server använder du Hanterad tjänstidentitet tillsammans med Azure Key Vault för att lagra och hämta autentiseringsuppgifter för Azure Database for MariaDB Server.  Se till att Key Vault mjuk borttagning har Aktiver ATS.

Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory (AD). Med hanterade identiteter kan du autentisera till vilken tjänst som helst som stöder Azure AD-autentisering, inklusive Key Vault utan autentiseringsuppgifter i din kod. Konfigurera hanterade identiteter: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm . Så här integrerar du med Azure Managed Identities: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity .



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault. 

Konfigurera inloggnings skannern: https://secdevtools.azurewebsites.net/helpcredscan.html

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [säkerhets kontroll: försvar mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Använd centralt hanterat program mot skadlig kod

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som har stöd för Azure-tjänster (till exempel Azure App Service), men det körs inte på kund innehållet.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som stöder Azure-tjänster (till exempel Azure Database for MariaDB Server), men den körs inte på kund innehåll.

Förskanna allt innehåll som laddas upp till Azure-resurser som inte är Compute, till exempel App Service, Data Lake Storage, Blob Storage, Azure Database for MariaDB Server osv. Microsoft kan inte komma åt dina data i dessa instanser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för skadlig program vara uppdateras

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som har stöd för Azure-tjänster (till exempel Azure Database for MariaDB Server), men den körs inte på kund innehåll.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [säkerhets kontroll: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: se till att vanlig automatisk säkerhets kopiering UPS

**Vägledning**: Azure Database for MariaDB tar fullständiga, differentiella och transaktions logg säkerhets kopior.  Azure Database for MariaDB skapar automatiskt Server säkerhets kopior och lagrar dem i användar konfiguration lokalt redundant eller Geo-redundant lagring. Säkerhetskopieringar kan användas för att återställa servern till en vald tidpunkt. Säkerhetskopiering och återställning är en viktig del i strategin för affärskontinuitet, eftersom de skyddar dina data från oavsiktlig skada eller borttagning.  Standard kvarhållningsperioden för säkerhets kopiering är sju dagar. Du kan också konfigurera det upp till 35 dagar. Alla säkerhetskopior krypteras med AES 256-bitars kryptering.

Förstå säkerhets kopieringar för MariaDB:  https://docs.microsoft.com/azure/mariadb/concepts-backup

Förstå MariaDB inledande konfiguration: https://docs.microsoft.com/azure/mariadb/tutorial-design-database-using-portal



**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: Azure Database for MariaDB skapar automatiskt Server säkerhets kopior och lagrar dem i användar konfiguration lokalt redundant eller Geo-redundant lagring. Säkerhetskopieringar kan användas för att återställa servern till en vald tidpunkt.  Säkerhetskopiering och återställning är en viktig del i strategin för affärskontinuitet, eftersom de skyddar dina data från oavsiktlig skada eller borttagning.

Om du använder Key Vault för data kryptering på klient sidan för data som lagras i din MariaDB-Server, se till att vanliga automatiserade säkerhets kopieringar av dina nycklar används.

Förstå säkerhets kopieringar för MariaDB:  https://docs.microsoft.com/azure/mariadb/concepts-backup

Så här säkerhetskopierar du Key Vault nycklar:  https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey


**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: i Azure Database for MariaDB utför du en återställning från den ursprungliga serverns säkerhets kopior för regelbundna tester av säkerhets kopieringar. Det finns två typer av återställning tillgängliga: återställning av tidpunkter och geo-återställning. Återställning av tidpunkt är tillgängligt med alternativ för redundans och skapar en ny server i samma region som den ursprungliga servern. Geo-återställning är bara tillgängligt om du har konfigurerat servern för Geo-redundant lagring och du kan återställa servern till en annan region.

Den uppskattade återställnings tiden beror på flera faktorer, till exempel databasens storlek, transaktions loggens storlek, nätverks bandbredden och det totala antalet databaser som återställs i samma region på samma tid. Återställnings tiden är vanligt vis mindre än 12 timmar.

Förstå säkerhets kopiering och återställning i Azure Database for MariaDB:  https://docs.microsoft.com/azure/mariadb/concepts-backup#restore


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: Azure Database for MariaDB tar fullständiga, differentiella och transaktions logg säkerhets kopior. Med dessa säkerhets kopieringar kan du återställa en server till alla tidpunkter inom den konfigurerade kvarhållningsperioden för säkerhets kopior. Standard kvarhållningsperioden för säkerhets kopiering är sju dagar. Du kan också konfigurera det upp till 35 dagar. Alla säkerhetskopior krypteras med AES 256-bitars kryptering.

Förstå säkerhets kopiering och återställning i Azure Database for MariaDB:  https://docs.microsoft.com/azure/mariadb/concepts-backup


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [säkerhets kontroll: incident svar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.

- Vägledning om hur du skapar en egen svars process för säkerhets incident: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/
- Microsoft Security Response Center: s uppbyggnad av en incident: https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/
- Kunden kan också utnyttja NISTs hanterings guide för dator säkerhet för att hjälpa till med att skapa egna incident svars planer: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final 

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen. 

Dessutom är det tydligt att markera prenumerationer (t. ex. produktion, icke-Prod.) med hjälp av taggar och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser, särskilt för bearbetning av känsliga data.  Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

- Säkerhets aviseringar i Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

- Använd taggar för att organisera dina Azure-resurser: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems funktioner för incident svar på en vanlig takt för att hjälpa till att skydda dina Azure-resurser. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- Se NIST: guide för test, utbildning och övnings program för IT-planer och funktioner: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en olagligt eller obehörig part. Granska incidenter när du är säker på att problemen är lösta.

- Så här ställer du in Azure Security Center säkerhets kontakt: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export för att identifiera risker för Azure-resurser. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviseringarna till Azure Sentinel.

- Så här konfigurerar du kontinuerlig export: https://docs.microsoft.com/azure/security-center/continuous-export
- Strömma aviseringar till Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för arbets flödes automatisering i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer för att skydda dina Azure-resurser.
    

Konfigurera automatisering av arbets flöde och Logic Apps: https://docs.microsoft.com/azure/security-center/workflow-automation

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [säkerhets kontroll: inträngande tester och röda team övningar](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att alla kritiska säkerhets brister upptäcks inom 60 dagar

**Vägledning**: Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Du hittar mer information om Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program, här:  https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../security/benchmarks/overview.md)
- Läs mer om [Azures säkerhets bas linjer](../security/benchmarks/security-baselines-overview.md)