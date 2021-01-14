---
title: Azures säkerhetsbaslinje för HDInsight
description: Azures säkerhetsbaslinje för HDInsight
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 09f08643ef0681533bbdb3e4b5e0f73013882361
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197259"
---
# <a name="azure-security-baseline-for-hdinsight"></a>Azures säkerhetsbaslinje för HDInsight

Azures säkerhets bas linje för HDInsight innehåller rekommendationer som hjälper dig att förbättra säkerhets position i din distribution.

Bas linjen för den här tjänsten hämtas från [Azures prestandatest version 1,0](../security/benchmarks/overview.md), som ger rekommendationer om hur du kan skydda dina moln lösningar i Azure med våra bästa praxis rikt linjer.

Mer information finns i [Översikt över Azure Security-bas linjer](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets kontroll: nätverks säkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: skydda resurser med hjälp av nätverks säkerhets grupper eller Azure-brandvägg på din Virtual Network

**Vägledning**: perimeter-säkerhet i Azure HDInsight uppnås via virtuella nätverk. En företags administratör kan skapa ett kluster i ett virtuellt nätverk och använda en nätverks säkerhets grupp (NSG) för att begränsa åtkomsten till det virtuella nätverket. Endast de tillåtna IP-adresserna i de inkommande reglerna för nätverks säkerhets gruppen kommer att kunna kommunicera med Azure HDInsight-klustret. Den här konfigurationen tillhandahåller perimeter-säkerhet. Alla kluster som distribueras i ett virtuellt nätverk har också en privat slut punkt som matchar en privat IP-adress i Virtual Network för privat HTTP-åtkomst till kluster-gatewayerna.

För att minska risken för data förlust via exfiltrering begränsar du utgående nätverks trafik för Azure HDInsight-kluster med hjälp av Azure-brandväggen.

Så här distribuerar du Azure HDInsight i en Virtual Network och skyddar med en nätverks säkerhets grupp: https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Så här begränsar du utgående trafik för Azure HDInsight-kluster med Azure-brand väggen: https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverkskort

**Vägledning**: Använd Azure Security Center och åtgärda rekommendationer för nätverks skydd för det virtuella nätverket, under nätet och nätverks säkerhets gruppen som används för att skydda ditt Azure HDInsight-kluster. Aktivera flödes loggar för nätverks säkerhets grupper (NSG) och skicka loggar till ett Azure Storage konto till trafik granskning. Du kan också skicka NSG Flow-loggar till en Azure Log Analytics-arbetsyta och använda Azure Trafikanalys för att ge insikter om trafikflöde i Azure-molnet. Några av fördelarna med Azure Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks fel konfigurationer.

Så här aktiverar du NSG Flow-loggar:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Så här aktiverar och använder du Azure-Trafikanalys:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Förstå nätverks säkerhet som tillhandahålls av Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: ej tillämpligt; Benchmark är avsett för Azure Apps-tjänster eller data bearbetnings resurser som är värdar för webb program.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: för skydd mot DDoS-attacker aktiverar du Azure DDoS standard-skydd på det virtuella nätverk där Azure HDInsight distribueras. Använd Azure Security Center integrerad Hot information för att neka kommunikation med kända skadliga eller oanvända Internet-IP-adresser.

Så här konfigurerar du DDoS-skydd:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Förstå Azure Security Center integrerad Hot information:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: registrera nätverks paket och flödes loggar

**Vägledning**: Aktivera flog för nätverks säkerhets gruppen (NSG) för NSG som är anslutna till det undernät som används för att skydda ditt Azure HDInsight-kluster. Registrera NSG flödes loggar i ett Azure Storage-konto för att generera flödes poster. Om det behövs för att undersöka avvikande aktivitet aktiverar du insamlingen av Azure Network Watcher-paket.

Så här aktiverar du NSG Flow-loggar:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Så här aktiverar du Network Watcher:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera Network-baserad intrångs identifiering/intrångs skydd system (ID/IP-adresser)

**Vägledning**: krav kan uppfyllas i Azure Security control ID 1,1; Distribuera Azure HDInsight-kluster till ett virtuellt nätverk och skydda med en nätverks säkerhets grupp (NSG).

Det finns flera beroenden för Azure HDInsight som kräver inkommande trafik. Inkommande hanterings trafik kan inte skickas via en brand Väggs enhet. Käll adresserna för nödvändig hanterings trafik är kända och publicerade. Skapa regler för nätverks säkerhets grupper med den här informationen för att tillåta trafik från enbart betrodda platser, skydda inkommande trafik till klustren.

För att minska risken för data förlust via exfiltrering begränsar du utgående nätverks trafik för Azure HDInsight-kluster med hjälp av Azure-brandväggen.

Så här distribuerar du HDInsight i en Virtual Network och skyddar med en nätverks säkerhets grupp: https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Förstå HDInsight-beroenden och brand Väggs användning: https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

HDInsight-hantering IP-adresser: https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: ej tillämpligt; Benchmark är avsett för Azure Apps-tjänster eller data bearbetnings resurser som är värdar för webb program.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: Använd taggar för virtuella nätverks tjänster för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper (NSG) som är kopplade till det undernät som ditt Azure HDInsight-kluster distribueras i. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet (t. ex. API Management) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

Förstå och använda service märken för Azure HDInsight:

https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för nätverks resurser som är relaterade till ditt Azure HDInsight-kluster. Använd Azure Policy alias i namn områdena "Microsoft. HDInsight" och "Microsoft. Network" om du vill skapa anpassade principer för granskning eller tillämpa nätverks konfigurationen för ditt Azure HDInsight-kluster.

Du kan också använda Azure-ritningar för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljö artefakter, till exempel Azure Resource Manager mallar, Azure RBAC-kontroller och principer, i en enda skiss definition. Använd enkelt skissen på nya prenumerationer och miljöer och finjustera kontroll och hantering genom versions hantering.

Så här visar du tillgängliga Azure Policy alias:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias Så här konfigurerar och hanterar du Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här skapar du en Azure Blueprint:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för nätverks säkerhets gruppen (NSG: er) och andra resurser som rör nätverks säkerhets-och trafikflödet som är associerade med ditt Azure HDInsight-kluster. För enskilda NSG-regler använder du fältet Beskrivning för att ange affärs behov och/eller varaktighet (osv.) för alla regler som tillåter trafik till/från ett nätverk.

Använd någon av de inbyggda Azure Policy definitionerna som är relaterade till taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och meddela dig om befintliga otaggade resurser.

Du kan använda Azure PowerShell eller Azure kommando rads gränssnitt (CLI) för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

Skapa och använda Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Så här skapar du ett virtuellt nätverk:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Så här skapar du en NSG med en säkerhets konfiguration:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar för nätverks resurser som är relaterade till dina Azure HDInsight-distributioner. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks resurser sker.

Så här visar och hämtar du Azure aktivitets logg händelser:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Så här skapar du aviseringar i Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets kontroll: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

**Vägledning**: Microsoft hanterar tids källor för Azure HDInsight-kluster komponenter, du kan uppdatera tidssynkroniseringen för dina beräknings distributioner.

Så här konfigurerar du tidssynkronisering för Azure Compute-resurser:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: du kan publicera ditt Azure HDInsight-kluster för att Azure monitor att samla in säkerhets data som genereras av klustret. Använd anpassade frågor för att identifiera och svara på hot i miljön. 

Så här publicerar du ett Azure HDInsight-kluster för att Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Så här skapar du anpassade frågor för ett Azure HDInsight-kluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Aktivera Azure Monitor för HDInsight-klustret, dirigera det till en Log Analytics arbets yta. Detta kommer att logga relevant kluster information och OS-mått för alla Azure HDInsight-klusternoder.

Så här aktiverar du loggning för HDInsight-kluster:

 https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Så här frågar du HDInsight-loggar:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

**Vägledning**: publicera Azure HDInsight-kluster till Azure Monitor. Se till att den Log Analytics arbets ytan som används har den logg kvarhållningsperiod som angetts enligt organisationens regler för efterlevnad.

Så här publicerar du ett Azure HDInsight-kluster för att Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Så här konfigurerar du Log Analytics bevarande period för arbets ytor:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: publicera Azure HDInsight-kluster till Azure Monitor. Kontrol lera att den Azure Log Analytics-arbetsyta som används har den logg kvarhållningsperiod som angetts enligt organisationens regler för efterlevnad.

Så här publicerar du ett Azure HDInsight-kluster för att Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Så här konfigurerar du Log Analytics bevarande period för arbets ytor:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: använd Azure Log Analytics Workspace-frågor för att fråga Azure HDInsight-loggar:

Så här skapar du anpassade frågor för Azure HDInsight-kluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: aktivera aviseringar för avvikande aktivitet

**Vägledning**: använd Azure Log Analytics-arbetsytan för att övervaka och Avisera om avvikande aktiviteter i säkerhets loggar och händelser relaterade till ditt Azure HDInsight-kluster.

Hantera aviseringar i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Så här aviserar du om Log Analytics-loggdata:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: Azure HDInsight levereras med clamscan förinstallerat och aktiverat för klusternoderna, men du måste hantera program varan och manuellt aggregera/övervaka alla loggar clamscan som skapas.

Förstå clamscan:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: implementera en lösning från tredje part för DNS-loggning.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

**Vägledning**: Konfigurera konsol loggning manuellt per nod.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [säkerhets kontroll: identitets-och Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: underhålla posten för det lokala administratörs kontot som skapas under kluster etableringen av Azure HDInsight-kluster samt andra konton som du skapar. Om Azure AD-integrering används har Azure AD dessutom inbyggda roller som måste tilldelas explicit och därför kan frågas. Använd Azure AD PowerShell-modulen för att utföra adhoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

Dessutom kan du använda rekommendationer för Azure Security Center identitets-och åtkomst hantering.

Så här hämtar du en katalog roll i Azure AD med PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole

Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember

Så här övervakar du identitet och åtkomst med Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: när du konfigurerar ett kluster kräver Azure att du skapar nya lösen ord för webb portalen och SSH-åtkomst (Secure Shell). Det finns inga standard lösen ord att ändra, men du kan ange olika lösen ord för SSH-och webb Portal åtkomst.

Ange lösen ord vid etablering av ett Azure HDInsight-kluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: integrera autentisering för Azure HDInsight-kluster med Azure Active Directory. Skapa principer och procedurer kring användningen av dedikerade administrativa konton.

Dessutom kan du använda rekommendationer för Azure Security Center identitets-och åtkomst hantering.

Så här integrerar du Azure HDInsight-autentisering med Azure Active Directory:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Så här övervakar du identitet och åtkomst med Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: Använd Azure HDInsight ID Broker för att logga in på Enterprise Security Package (ESP)-kluster genom att använda Multi-Factor Authentication, utan att ange lösen ord. Om du redan har loggat in på andra Azure-tjänster, till exempel Azure Portal, kan du logga in på ditt Azure HDInsight-kluster med enkel inloggning (SSO).

Så här aktiverar du Azure HDInsight ID Broker:

https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker#enable-hdinsight-id-broker

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory baserad åtkomst

**Vägledning**: Aktivera Azure AD MFA och följ Azure Security Center rekommendationer för identitets-och åtkomst hantering. Azure HDInsight-kluster med Enterprise Security Package konfigurerad kan anslutas till en domän så att domän användare kan använda sina domänautentiseringsuppgifter för att autentisera med klustren och köra stora data jobb. När du autentiserar med Multi-Factor Authentication (MFA) aktive rad, kommer användarna att få en andra autentiseringsmetod.

Så här aktiverar du MFA i Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Övervaka identitet och åtkomst i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd Paw (arbets stationer med privilegie rad åtkomst) med Multi-Factor Authentication (MFA) konfigurerat för att logga in på och konfigurera dina Azure HDInsight-kluster och relaterade resurser.

Lär dig mer om arbets stationer med privilegie rad åtkomst:

https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

Så här aktiverar du MFA i Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkt aktivitet från administrativa konton

**Vägledning**: Azure HDInsight-kluster med Enterprise Security Package konfigurerad kan anslutas till en domän så att domän användare kan använda sina domänautentiseringsuppgifter för att autentisera sig. Du kan använda Azure Active Directory (AAD) säkerhets rapporter för generering av loggar och varningar när misstänkt eller osäker aktivitet inträffar i AAD-miljön. Använd Azure Security Center för att övervaka identitets-och åtkomst aktiviteter.

Så här identifierar du AAD-användare som har flaggats för riskfylld aktivitet:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Så här övervakar du användarnas identitets-och åtkomst aktiviteter i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Azure HDInsight-kluster med Enterprise Security Package konfigurerad kan anslutas till en domän så att domän användare kan använda sina domänautentiseringsuppgifter för att autentisera sig. Använd namngivna platser för villkorlig åtkomst för att tillåta åtkomst från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner.

Så här konfigurerar du namngivna platser i Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: använda Azure Active Directory (AAD) som central autentiserings-och auktoriserings system. AAD skyddar data genom att använda stark kryptering för data i vila och under överföring. AAD är också salter, hashar och lagrar användarautentiseringsuppgifter på ett säkert sätt.

Azure HDInsight-kluster med Enterprise Security Package (ESP) konfigurerat kan anslutas till en domän så att domän användare kan använda sina domänautentiseringsuppgifter för att autentisera med klustren.

Så här skapar och konfigurerar du en AAD-instans:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Så här konfigurerar du Enterprise Security Package med Azure Active Directory Domain Services i Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: använda Azure Active Directory (AAD)-autentisering med ditt Azure HDInsight-kluster. AAD innehåller loggar som hjälper till att identifiera inaktuella konton. Dessutom kan du använda Azure Identity Access-granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användarens åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst. 

Så här använder du granskningar av Azure Identity Access:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: övervaka försök att komma åt inaktiverade konton

**Vägledning**: Använd Azure Active Directory (AAD)-inloggning och gransknings loggar för att övervaka försök att komma åt inaktiverade konton. dessa loggar kan integreras i alla SIEM/övervaknings verktyg från tredje part.

Du kan effektivisera den här processen genom att skapa diagnostikinställningar för AAD-användarkonton, skicka gransknings loggarna och inloggnings loggarna till en Azure Log Analytics-arbetsyta. Konfigurera önskade aviseringar i Azure Log Analytics-arbetsytan.

Så här integrerar du Azure-aktivitets loggar i Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: avisering om beteende för beteende för konto inloggning

**Vägledning**: Azure HDInsight-kluster med Enterprise Security Package (ESP) som kon figurer ATS kan anslutas till en domän så att domän användare kan använda sina domänautentiseringsuppgifter för att autentisera med klustren.  Använd Azure Active Directory (AAD) risk identifiering och identitets skydds funktion för att konfigurera automatiserade svar på identifierade misstänkta åtgärder relaterade till användar identiteter. Dessutom kan du mata in data i Azure Sentinel för ytterligare undersökning.

Så här visar du AAD-riskfyllda inloggningar:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Så här konfigurerar och aktiverar du principer för identitets skydds risker:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: inte tillgänglig; Customer Lockbox ännu inte stöd för Azure HDInsight.

Lista över Customer Lockbox tjänster som stöds: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [säkerhets kontroll: data skydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för resurser som är relaterade till dina Azure HDInsight-distributioner för att spåra Azure-resurser som lagrar eller bearbetar känslig information.

Skapa och använda Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera separata prenumerationer och/eller hanterings grupper för utveckling, testning och produktion. Azure HDInsight-kluster och associerade lagrings konton bör åtskiljas av ett virtuellt nätverk/undernät, taggas på lämpligt sätt och skyddas inom en nätverks säkerhets grupp (NSG) eller Azure-brandvägg. Kluster data ska finnas i ett skyddat Azure Storage konto eller Azure Data Lake Storage (gen1 eller Gen2).

Välj lagrings alternativ för ditt Azure HDInsight-kluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

Så här skyddar du Azure Data Lake Storage:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

Skydda Azure Storage-konton:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: för Azure HDInsight-kluster som lagrar eller bearbetar känslig information markerar du klustret och relaterade resurser som känsliga med taggar. För att minska risken för data förlust via exfiltrering begränsar du utgående nätverks trafik för Azure HDInsight-kluster med hjälp av Azure-brandväggen.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

Så här begränsar du utgående trafik för Azure HDInsight-kluster med Azure-brand väggen:

https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

Förstå kundens data skydd i Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: kryptera all känslig information under överföring. Se till att alla klienter som ansluter till ditt Azure HDInsight-kluster eller kluster data lager (Azure Storage-konton eller Azure Data Lake Storage Gen1-Gen2) kan förhandla TLS 1,2 eller senare. Microsoft Azure resurser förhandlar TLS 1,2 som standard. 

Förstå Azure Data Lake Storage kryptering i överföring:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

Förstå Azure Storage konto kryptering i överföring:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure Storage-eller beräknings resurser. Implementera en lösning från tredje part om det krävs för efterlevnad.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

Förstå kundens data skydd i Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser

**Vägledning**: med Azure HDInsight Enterprise Security Package (ESP) kan du använda Apache Ranger för att skapa och hantera detaljerade åtkomst kontroll-och data döljande-principer för dina data som lagras i filer, mappar, databaser, tabeller och rader/kolumner. Hadoop-administratören kan konfigurera rollbaserad åtkomst kontroll (RBAC) för att skydda Apache Hive, HBase, Kafka och Spark med dessa plugin-program i Apache Ranger.

Genom att konfigurera RBAC-principer med Apache Ranger kan du associera behörigheter med en roll i organisationen. Detta skikt gör det lättare att se till att människor bara har de behörigheter som krävs för att utföra sina uppgifter.

Enterprise Security Package konfigurationer med Azure Active Directory Domain Services i HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Översikt över företags säkerhet i Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview



**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: för Azure HDInsight-kluster som lagrar eller bearbetar känslig information markerar du klustret och relaterade resurser som känsliga med taggar. Funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure Storage-eller beräknings resurser. Implementera en lösning från tredje part om det krävs för efterlevnad.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

Förstå kundens data skydd i Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: om du använder Azure SQL Database för att lagra Apache Hive och Apache Oozie-metadata bör du se till att SQL-data förblir krypterade. För Azure Storage konton och Data Lake Storage (gen1 eller Gen2) rekommenderas det att låta Microsoft hantera dina krypterings nycklar, men du har möjlighet att hantera dina egna nycklar.

Hantera krypterings nycklar för Azure Storage-konton:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

Så här skapar du Azure Data Lake Storage med hjälp av Kundhanterade krypterings nycklar:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal

Förstå kryptering för Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview#data-encryption

Så här konfigurerar du transparent datakryptering för SQL Database med hjälp av Kundhanterade nycklar:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Konfigurera diagnostikinställningar för Azure Storage konton som är kopplade till Azure HDInsight-kluster för att övervaka och logga alla CRUD-åtgärder mot kluster data. Aktivera granskning för alla lagrings konton eller Data Lake lager som är associerade med Azure HDInsight-klustret.

Så här aktiverar du ytterligare loggning/granskning för ett Azure Storage-konto:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

Så här aktiverar du ytterligare loggning/granskning för Azure Data Lake Storage:

https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [säkerhets kontroll: sårbarhets hantering](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: implementera en sårbarhets hanterings lösning från tredje part.

Om du har en Rapid7, Qualys eller någon annan plattforms prenumeration för sårbarhets hantering kan du använda skript åtgärder för att installera sårbarhets bedömnings agenter på dina Azure HDInsight-klusternoder och hantera noderna via respektive Portal.

Så här installerar du Rapid7-agenten manuellt:

https://insightvm.help.rapid7.com/docs/install

Så här installerar du Qualys-agenten manuellt:

https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf

Så här använder du skript åtgärder:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: automatiska system uppdateringar har Aktiver ATS för klusternoder, men du måste regelbundet starta om klusternoder för att säkerställa att uppdateringarna tillämpas.

Microsoft för att underhålla och uppdatera grundläggande Azure HDInsight-Node-avbildningar.

Konfigurera operativ systemets uppdaterings schema för HDInsight-kluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Distribuera automatisk hanterings lösning för program uppdatering från tredje part

**Vägledning**: Använd skript åtgärder eller andra mekanismer för att korrigera dina Azure HDInsight-kluster. Nya kluster kommer alltid att ha de senaste tillgängliga uppdateringarna, inklusive de senaste säkerhets korrigeringarna.

Så här konfigurerar du en uppdatering av operativ systemets uppdaterings schema för Linux-baserade Azure HDInsight-kluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

Så här använder du skript åtgärder:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: implementera en lösning för sårbarhets hantering från tredje part som har möjlighet att jämföra sårbara genomsökningar över tid. Om du har en Rapid7-eller Qualys-prenumeration kan du använda leverantörens Portal för att visa och jämföra genomsökningar med säkerhets risker från säkerhets kopiering.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: Använd ett gemensamt risk bedömnings program (t. ex. vanliga sårbarhets bedömnings system) eller standard risk klassificeringarna som tillhandahålls av genomsöknings verktyget från tredje part.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [säkerhets kontroll: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Använd Azure Asset Discovery

**Vägledning**: Använd Azure Resource Graph för att fråga/identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk, portar och protokoll osv.), inklusive Azure HDInsight-kluster, i dina prenumerationer.  Se till att du har rätt (Läs) behörigheter i din klient och kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via resurs diagram, rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser som går framåt.

Så här skapar du frågor med Azure Resource Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Så här visar du dina Azure-prenumerationer:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription

Förstå Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

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

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Använd Azure Resource Graph för att fråga/identifiera resurser i dina prenumerationer. Se till att alla Azure-resurser som finns i miljön är godkända.

Så här konfigurerar och hanterar du Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här skapar du frågor med Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: implementera en lösning från tredje part för att övervaka klusternoder för program som inte är godkända.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: Använd Azure Resource Graph för att fråga/identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk, portar och protokoll osv.), inklusive Azure HDInsight-kluster, i dina prenumerationer.  Ta bort alla ej godkända Azure-resurser som du identifierar. För Azure HDInsight-klusternoder implementerar du en lösning från tredje part för att ta bort eller Avisera om ej godkänd program vara.

Så här skapar du frågor med Azure Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: för Azure HDInsight-klusternoder implementerar du en lösning från tredje part för att förhindra att obehöriga program körs.


**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Så här konfigurerar och hanterar du Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här nekar du en speciell resurs typ med Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="610-implement-approved-application-list"></a>6,10: implementera lista över godkända program

**Vägledning**: för Azure HDInsight-klusternoder implementerar du en lösning från tredje part för att förhindra att obehöriga filtyper körs.


**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: begränsa användarnas möjlighet att interagera med Azures resurs hanterare via skript

**Vägledning**: Använd villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: ej tillämpligt; Detta gäller inte för Azure HDInsight eftersom användare (icke-administratörer) av klustret inte behöver åtkomst till de enskilda noderna för att köra jobb. Kluster administratören har rot åtkomst till alla klusternoder.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvars område**: inte tillämpligt

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: ej tillämpligt; Benchmark är avsett för Azure Apps-tjänster eller data bearbetnings resurser som är värdar för webb program.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvars område**: inte tillämpligt

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [säkerhets kontroll: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. HDInsight" för att skapa anpassade principer för att granska eller tillämpa nätverks konfigurationen för ditt HDInsight-kluster.

Så här visar du tillgängliga Azure Policy alias:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias

Så här konfigurerar och hanterar du Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

**Vägledning**: Azure HDInsight operativ Systems avbildningar som hanteras och underhålls av Microsoft. Kund som ansvarar för att implementera säkra konfigurationer för klusternodernas operativ system. 


**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns] för att genomdriva säkra inställningar för dina Azure HDInsight-kluster och relaterade resurser.

Så här konfigurerar och hanterar du Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Förstå Azure Policys effekter:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: Azure HDInsight operativ Systems avbildningar som hanteras och underhålls av Microsoft. Kund som ansvarar för att implementera tillstånds konfigurationen på operativ system nivå.


**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: om du använder anpassade Azure policys definitioner använder du Azure DevOps eller Azure databaser för att lagra och hantera din kod på ett säkert sätt.

[Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

[Dokumentation om Azure databaser](/azure/devops/repos/index?view=azure-devops&preserve-view=true)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: ej tillämpligt; anpassade avbildningar som inte gäller för Azure HDInsight.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvars område**: inte tillämpligt

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: Distribuera hanterings verktyg för system konfiguration

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. HDInsight" för att skapa anpassade principer för avisering, granskning och system konfiguration. Dessutom kan du utveckla en process och pipeline för att hantera princip undantag.

Så här konfigurerar och hanterar du Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: distribuera system konfigurations hanterings verktyg för operativ system

**Vägledning**: implementera en lösning från tredje part som upprätthåller önskat tillstånd för dina kluster Node-operativsystem.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementera automatisk konfigurations övervakning för Azure-tjänster

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. HDInsight" för att skapa anpassade principer för granskning eller framtvinga konfigurationen av ditt HDInsight-kluster.

[Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias)

[Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: implementera en lösning från tredje part för att övervaka status för dina kluster Node-operativsystem.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Azure HDInsight innehåller Bring Your Own Key (BYOK) stöd för Apache Kafka. Med den här funktionen kan du äga och hantera nycklarna som används för att kryptera data i vila.

Alla hanterade diskar i Azure HDInsight skyddas med Azure Storage Service Encryption (SSE). Som standard krypteras data på dessa diskar med hjälp av Microsoft-hanterade nycklar. Om du aktiverar BYOK anger du krypterings nyckeln för Azure HDInsight för att använda och hantera den med hjälp av Azure Key Vault.

Key Vault kan också användas med Azure HDInsight-distributioner för att hantera nycklar för kluster lagring (Azure Storage-konton och Azure Data Lake Storage)

Så här använder du din egen nyckel för Apache Kafka på Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

Hantera krypterings nycklar för Azure Storage-konton:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: hanterade identiteter kan användas i Azure HDInsight för att tillåta att klustren kommer åt Azure Active Directory domän tjänster, åtkomst Azure Key Vault eller åtkomst till filer i Azure Data Lake Storage Gen2.

Förstå hanterade identiteter med Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: om du använder en kod som är relaterad till din Azure HDInsight-distribution kan du implementera autentiseringsuppgifterna för autentisering för att identifiera autentiseringsuppgifter i koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault. 

Konfigurera inloggnings skannern:

https://secdevtools.azurewebsites.net/helpcredscan.html

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [säkerhets kontroll: försvar mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Använd centralt hanterat program mot skadlig kod

**Vägledning**: Azure HDInsight levereras med clamscan förinstallerat och aktiverat för klusternoderna, men du måste hantera program varan och manuellt aggregera/övervaka alla loggar clamscan som skapas.

Förstå clamscan för Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Microsoft Antimalware är aktiverat på den underliggande värden som har stöd för Azure-tjänster, men det körs inte på kund innehåll.

Genomsök alla filer som laddas upp till Azure-resurser som är relaterade till Azure HDInsight-klustrets distribution, till exempel Data Lake Storage, Blob Storage osv. Microsoft kan inte komma åt kund information i dessa instanser.

Förstå Microsoft Antimalware för Azure Cloud Services och Virtual Machines:

 https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för skadlig program vara uppdateras

**Vägledning**: Azure HDInsight levereras med clamscan förinstallerat och aktiverat för klusternod avbildningar. Clamscan kommer att utföra motor-och definitions uppdateringar automatiskt, men agg regering och hantering av loggar måste utföras manuellt.

Förstå clamscan för Azure Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [säkerhets kontroll: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: se till att vanlig automatisk säkerhets kopiering UPS

**Vägledning**: när du använder ett Azure Storage konto för data lagret HDInsight-kluster väljer du lämpligt alternativ för REDUNDANS (LRS, ZRS, GRS, RA-GRS).  När du använder ett Azure SQL Database för Azure HDInsight-klustrets data lager konfigurerar du aktiv geo-replikering.

Så här konfigurerar du redundans för Azure Storage-konton:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Så här konfigurerar du redundans för Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: när du använder ett Azure Storage-konto för Azure HDInsight-klustrets data lager väljer du lämpligt alternativ för REDUNDANS (LRS, ZRS, GRS, RA-GRS). Om du använder Azure Key Vault för någon del av din Azure HDInsight-distribution kontrollerar du att nycklarna säkerhets kopie ras.

Välj lagrings alternativ för ditt Azure HDInsight-kluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

Så här konfigurerar du redundans för Azure Storage-konton:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Så här säkerhetskopierar du Key Vault nycklar i Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: om Azure Key Vault används med din Azure HDInsight-distribution kan du testa att återställa Kundhanterade nycklar.

Så här använder du din egen nyckel för Apache Kafka på Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

Så här återställer du Key Vault-nycklar i Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: om Azure Key Vault används med din Azure HDInsight-distribution aktiverar du Soft-Delete i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.

Så här aktiverar du Soft-Delete i Azure Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [säkerhets kontroll: incident svar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: kontrol lera att det finns skriftliga svars planer för incidenter som definierar personal roller och faser för incident hantering/hantering.

Konfigurera automatisering av arbets flöden i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till aviseringar, som hjälper dig att prioritera i vilken ordning du deltar i varje avisering, så att när en resurs komprometteras kan du komma åt den direkt. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems incident svars funktioner på en vanlig takt. Identifiera svaga punkter och luckor och ändra planen efter behov. Se NIST: guide för test, utbildning och övnings program för IT-planer och funktioner:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en olagligt eller obehörig part.

Så här ställer du in Azure Security Center säkerhets kontakt:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviserings indikatorn.

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

*Mer information finns i [säkerhets kontroll: inträngande tester och röda team övningar](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att alla kritiska säkerhets brister upptäcks inom 60 dagar

**Vägledning**: Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Du hittar mer information om Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft Managed Cloud Infrastructure, tjänster och program, här: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../security/benchmarks/overview.md)
- Läs mer om [Azures säkerhets bas linjer](../security/benchmarks/security-baselines-overview.md)