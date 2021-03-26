---
title: Azures säkerhets bas linje för HDInsight
description: Säkerhets bas linjen för HDInsight ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c5ffdecf768be0962950bb3691dbb11fb0e70120
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565018"
---
# <a name="azure-security-baseline-for-hdinsight"></a>Azures säkerhets bas linje för HDInsight

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 1,0](../security/benchmarks/overview-v1.md) till HDInsight. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure.
Innehållet grupperas efter **säkerhets kontrollerna** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för HDInsight. **Kontroller** som inte gäller för HDInsight har uteslutits.

 
Om du vill se hur HDInsight fullständigt mappar till Azures säkerhets benchmark, se den [fullständiga mappnings filen för HDInsight-säkerhet](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: perimeter-säkerhet i Azure HDInsight uppnås via virtuella nätverk. En företags administratör kan skapa ett kluster i ett virtuellt nätverk och använda en nätverks säkerhets grupp (NSG) för att begränsa åtkomsten till det virtuella nätverket. Endast de tillåtna IP-adresserna i de inkommande reglerna för nätverks säkerhets gruppen kommer att kunna kommunicera med Azure HDInsight-klustret. Den här konfigurationen tillhandahåller perimeter-säkerhet. Alla kluster som distribueras i ett virtuellt nätverk har också en privat slut punkt som matchar en privat IP-adress i Virtual Network för privat HTTP-åtkomst till kluster-gatewayerna.

För att minska risken för data förlust via exfiltrering begränsar du utgående nätverks trafik för Azure HDInsight-kluster med hjälp av Azure-brandväggen.

- [Så här distribuerar du Azure HDInsight i en Virtual Network och skyddar med en nätverks säkerhets grupp](hdinsight-create-virtual-network.md)

- [Begränsa utgående trafik för Azure HDInsight-kluster med Azure-brandvägg](hdinsight-restrict-outbound-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverks gränssnitt

**Vägledning**: Använd Azure Security Center och åtgärda rekommendationer för nätverks skydd för det virtuella nätverket, under nätet och nätverks säkerhets gruppen som används för att skydda ditt Azure HDInsight-kluster. Aktivera flödes loggar för nätverks säkerhets grupper (NSG) och skicka loggar till ett Azure Storage konto till trafik granskning. Du kan också skicka NSG Flow-loggar till en Azure Log Analytics-arbetsyta och använda Azure Trafikanalys för att ge insikter i trafikflöde i Azure-molnet. Några av fördelarna med Azure Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks fel konfigurationer.

- [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Azure Trafikanalys](../network-watcher/traffic-analytics.md)

- [Förstå nätverks säkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: för skydd mot DDoS-attacker aktiverar du Azure DDoS standard-skydd på det virtuella nätverk där Azure HDInsight distribueras. Använd Azure Security Center integrerad Hot information för att neka kommunikation med kända skadliga eller oanvända Internet-IP-adresser.

- [Så här konfigurerar du DDoS-skydd](../ddos-protection/manage-ddos-protection.md)

- [Förstå Azure Security Center integrerad Hot information](../security-center/azure-defender.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="15-record-network-packets"></a>1,5: registrera nätverks paket

**Vägledning**: Aktivera flödes loggar för nätverks säkerhets grupper (NSG) för NSG som är kopplade till det undernät som används för att skydda ditt Azure HDInsight-kluster. Registrera NSG flödes loggar i ett Azure Storage-konto för att generera flödes poster. Om det behövs för att undersöka avvikande aktivitet aktiverar du insamlingen av Azure Network Watcher-paket.

- [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar du Network Watcher](../network-watcher/network-watcher-create.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera nätverksbaserade intrångs identifiering/system för skydd mot intrång (ID/IP-adresser)

**Vägledning**: krav kan uppfyllas i Azure Security control ID 1,1; Distribuera Azure HDInsight-kluster till ett virtuellt nätverk och skydda med en nätverks säkerhets grupp (NSG).

Det finns flera beroenden för Azure HDInsight som kräver inkommande trafik. Inkommande hanterings trafik kan inte skickas via en brand Väggs enhet. Käll adresserna för nödvändig hanterings trafik är kända och publicerade. Skapa regler för nätverks säkerhets grupper med den här informationen för att tillåta trafik från enbart betrodda platser, skydda inkommande trafik till klustren.

För att minska risken för data förlust via exfiltrering begränsar du utgående nätverks trafik för Azure HDInsight-kluster med hjälp av Azure-brandväggen.

- [Så här distribuerar du HDInsight i en Virtual Network och skyddar med en nätverks säkerhets grupp](hdinsight-create-virtual-network.md)

- [Förstå HDInsight-beroenden och brand Väggs användning](hdinsight-restrict-outbound-traffic.md)

- [IP-adresser för HDInsight-hantering](hdinsight-management-ip-addresses.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: Använd taggar för virtuella nätverks tjänster för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper (NSG) som är kopplade till det undernät som ditt Azure HDInsight-kluster distribueras i. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet (t. ex. API Management) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

- [Förstå och använda service märken för Azure HDInsight](../virtual-network/network-security-groups-overview.md#service-tags)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för nätverks resurser som är relaterade till ditt Azure HDInsight-kluster. Använd Azure Policy alias i namn områdena "Microsoft. HDInsight" och "Microsoft. Network" om du vill skapa anpassade principer för granskning eller tillämpa nätverks konfigurationen för ditt Azure HDInsight-kluster.

Du kan också använda Azure-ritningar för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljö artefakter, till exempel Azure Resource Manager mallar, Azure RBAC-kontroller och principer, i en enda skiss definition. Använd enkelt skissen på nya prenumerationer och miljöer och finjustera kontroll och hantering genom versions hantering.

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du en Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för nätverks säkerhets gruppen (NSG: er) och andra resurser som rör nätverks säkerhets-och trafikflödet som är associerade med ditt Azure HDInsight-kluster. För enskilda NSG-regler använder du fältet Beskrivning för att ange affärs behov och/eller varaktighet (osv.) för alla regler som tillåter trafik till/från ett nätverk.

Använd någon av de inbyggda Azure Policy definitionerna som är relaterade till taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och meddela dig om befintliga otaggade resurser.

Du kan använda Azure PowerShell eller Azure kommando rads gränssnitt (CLI) för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

- [Så här skapar du ett virtuellt nätverk](../virtual-network/quick-create-portal.md)

- [Så här skapar du en NSG med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar för nätverks resurser som är relaterade till dina Azure HDInsight-distributioner. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks resurser sker.

- [Visa och hämta Azure aktivitets logg händelser](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets benchmark för Azure: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: du kan publicera ditt Azure HDInsight-kluster för att Azure monitor att samla in säkerhets data som genereras av klustret. Använd anpassade frågor för att identifiera och svara på hot i miljön. 

- [Så här publicerar du ett Azure HDInsight-kluster till Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Så här skapar du anpassade frågor för ett Azure HDInsight-kluster](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Aktivera Azure Monitor för HDInsight-klustret, dirigera det till en Log Analytics arbets yta. Detta kommer att logga relevant kluster information och OS-mått för alla Azure HDInsight-klusternoder.

- [Så här aktiverar du loggning för HDInsight-kluster](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Så här frågar du HDInsight-loggar](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

**Vägledning**: publicera Azure HDInsight-kluster till Azure Monitor. Se till att den Log Analytics arbets ytan som används har den logg kvarhållningsperiod som angetts enligt organisationens regler för efterlevnad.

- [Så här publicerar du ett Azure HDInsight-kluster till Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Så här konfigurerar du Log Analytics bevarande period för arbets ytor](../azure-monitor/logs/manage-cost-storage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: publicera Azure HDInsight-kluster till Azure Monitor. Kontrol lera att den Azure Log Analytics-arbetsyta som används har den logg kvarhållningsperiod som angetts enligt organisationens regler för efterlevnad.

- [Så här publicerar du ett Azure HDInsight-kluster till Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Så här konfigurerar du Log Analytics bevarande period för arbets ytor](../azure-monitor/logs/manage-cost-storage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: använd Azure Log Analytics Workspace-frågor för att fråga Azure HDInsight-loggar:

- [Så här skapar du anpassade frågor för Azure HDInsight-kluster](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: använd Azure Log Analytics-arbetsytan för att övervaka och Avisera om avvikande aktiviteter i säkerhets loggar och händelser relaterade till ditt Azure HDInsight-kluster.

- [Hantera aviseringar i Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Så här aviserar du om Log Analytics-loggdata](../azure-monitor/alerts/tutorial-response.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: Azure HDInsight levereras med clamscan förinstallerat och aktiverat för klusternoderna, men du måste hantera program varan och manuellt aggregera/övervaka alla loggar clamscan som skapas.

- [Förstå clamscan](./hdinsight-faq.md#security-and-certificates)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: implementera en lösning från tredje part för DNS-loggning.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

**Vägledning**: Konfigurera konsol loggning manuellt per nod.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security benchmark: identitet och Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: underhålla posten för det lokala administratörs kontot som skapas under kluster etableringen av Azure HDInsight-kluster samt andra konton som du skapar. Om Azure Active Directory (Azure AD)-integrering används, har Azure AD dessutom inbyggda roller som måste tilldelas explicit och som därför kan frågas. Använd Azure AD PowerShell-modulen för att utföra adhoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

Dessutom kan du använda rekommendationer för Azure Security Center identitets-och åtkomst hantering.

- [Så här hämtar du en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Övervaka identitet och åtkomst med Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: när du konfigurerar ett kluster kräver Azure att du skapar nya lösen ord för webb portalen och SSH-åtkomst (Secure Shell). Det finns inga standard lösen ord att ändra, men du kan ange olika lösen ord för SSH-och webb Portal åtkomst.

- [Ange lösen ord när du konfigurerar ett Azure HDInsight-kluster](hdinsight-hadoop-linux-use-ssh-unix.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: integrera autentisering för Azure HDInsight-kluster med Azure Active Directory (Azure AD). Skapa principer och procedurer kring användningen av dedikerade administrativa konton.

Dessutom kan du använda rekommendationer för Azure Security Center identitets-och åtkomst hantering.

- [Så här integrerar du Azure HDInsight-autentisering med Azure AD](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- [Övervaka identitet och åtkomst med Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Använd Azure Active Directory enkel inloggning (SSO)

**Vägledning**: Använd Azure HDInsight ID Broker för att logga in på Enterprise Security Package (ESP)-kluster genom att använda multifaktorautentisering, utan att ange lösen ord. Om du redan har loggat in på andra Azure-tjänster, till exempel Azure Portal, kan du logga in på ditt Azure HDInsight-kluster med enkel inloggning (SSO).

- [Så här aktiverar du Azure HDInsight ID Broker](./domain-joined/identity-broker.md#enable-hdinsight-id-broker)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory-baserad åtkomst

**Vägledning**: Aktivera Azure Active Directory (Azure AD) multifaktorautentisering och Azure Security Center Följ rekommendationerna för identitets-och åtkomst hantering. Azure HDInsight-kluster med Enterprise Security Package konfigurerad kan anslutas till en domän så att domän användare kan använda sina domänautentiseringsuppgifter för att autentisera med klustren och köra stora data jobb. När du autentiserar med multifaktorautentisering aktive rad, kan användarna ange en andra autentiseringsmetod.

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd Paw (Privileged Access Workstation) med multifaktorautentisering konfigurerad för att logga in på och konfigurera dina Azure HDInsight-kluster och relaterade resurser.

- [Lär dig mer om arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: Azure HDInsight-kluster med Enterprise Security Package konfigurerad kan anslutas till en domän så att domän användare kan använda sina domänautentiseringsuppgifter för att autentisera sig. Du kan använda Azure Active Directory (Azure AD) säkerhets rapporter för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i Azure AD-miljön. Använd Azure Security Center för att övervaka identitets-och åtkomst aktiviteter.

- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](../active-directory/identity-protection/overview-identity-protection.md)

- [Övervaka användarnas identitets-och åtkomst aktiviteter i Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Azure HDInsight-kluster med Enterprise Security Package konfigurerad kan anslutas till en domän så att domän användare kan använda sina domänautentiseringsuppgifter för att autentisera sig. Använd namngivna platser för villkorlig åtkomst för att tillåta åtkomst från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner.

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: använda Azure Active Directory (Azure AD) som central autentiserings-och auktoriserings system. Azure AD skyddar data med stark kryptering för data i vila och under överföring. Azure AD innehåller även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

Azure HDInsight-kluster med Enterprise Security Package (ESP) konfigurerat kan anslutas till en domän så att domän användare kan använda sina domänautentiseringsuppgifter för att autentisera med klustren.

- [Skapa och konfigurera en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Så här konfigurerar du Enterprise Security Package med Azure AD Domain Services i Azure HDInsight](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: använda Azure Active Directory (Azure AD)-autentisering med ditt Azure HDInsight-kluster. Azure AD innehåller loggar som hjälper till att identifiera inaktuella konton. Dessutom kan du använda Azure Identity Access-granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användarens åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

- [Så här använder du granskningar av Azure Identity Access](../active-directory/governance/access-reviews-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: Använd Azure Active Directory (Azure AD)-inloggning och gransknings loggar för att övervaka försök att komma åt inaktiverade konton. dessa loggar kan integreras i alla SIEM/övervaknings verktyg från tredje part.

Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton, skicka gransknings loggarna och inloggnings loggarna till en Azure Log Analytics-arbetsyta. Konfigurera önskade aviseringar i Azure Log Analytics-arbetsytan.

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: varning vid inloggnings beteende för konto

**Vägledning**: Azure HDInsight-kluster med Enterprise Security Package (ESP) som kon figurer ATS kan anslutas till en domän så att domän användare kan använda sina domänautentiseringsuppgifter för att autentisera med klustren. Använd Azure Active Directory (Azure AD) risk identifiering och identitets skydds funktion för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter. Dessutom kan du mata in data i Azure Sentinel för ytterligare undersökning.

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: inte tillgänglig; Customer Lockbox ännu inte stöd för Azure HDInsight.

- [Lista över Customer Lockbox tjänster som stöds](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för resurser som är relaterade till dina Azure HDInsight-distributioner för att spåra Azure-resurser som lagrar eller bearbetar känslig information.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera separata prenumerationer och/eller hanterings grupper för utveckling, testning och produktion. Azure HDInsight-kluster och associerade lagrings konton bör åtskiljas av ett virtuellt nätverk/undernät, taggas på lämpligt sätt och skyddas inom en nätverks säkerhets grupp (NSG) eller Azure-brandvägg. Kluster data ska finnas i ett skyddat Azure Storage konto eller Azure Data Lake Storage (gen1 eller Gen2).

- [Välj lagrings alternativ för ditt Azure HDInsight-kluster](hdinsight-hadoop-compare-storage-options.md)

- [Skydda Azure Data Lake Storage](../data-lake-store/data-lake-store-security-overview.md)

- [Skydda Azure Storage-konton](../storage/blobs/security-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: för Azure HDInsight-kluster som lagrar eller bearbetar känslig information markerar du klustret och relaterade resurser som känsliga med taggar. För att minska risken för data förlust via exfiltrering begränsar du utgående nätverks trafik för Azure HDInsight-kluster med hjälp av Azure-brandväggen.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Begränsa utgående trafik för Azure HDInsight-kluster med Azure-brandvägg](hdinsight-restrict-outbound-traffic.md)

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: kryptera all känslig information under överföring. Se till att alla klienter som ansluter till ditt Azure HDInsight-kluster eller kluster data lager (Azure Storage-konton eller Azure Data Lake Storage Gen1-Gen2) kan förhandla TLS 1,2 eller senare. Microsoft Azure resurser förhandlar TLS 1,2 som standard. 

- [Förstå Azure Data Lake Storage kryptering under överföring](../data-lake-store/data-lake-store-security-overview.md)

- [Förstå Azure Storage konto kryptering vid överföring](../storage/blobs/security-recommendations.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure Storage-eller beräknings resurser. Implementera en lösning från tredje part om det krävs för efterlevnad.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser 

**Vägledning**: med Azure HDInsight Enterprise Security Package (ESP) kan du använda Apache Ranger för att skapa och hantera detaljerade åtkomst kontroll-och data döljande-principer för dina data som lagras i filer, mappar, databaser, tabeller och rader/kolumner. Hadoop-administratören kan konfigurera rollbaserad åtkomst kontroll (RBAC) för att skydda Apache Hive, HBase, Kafka och Spark med dessa plugin-program i Apache Ranger.

Genom att konfigurera RBAC-principer med Apache Ranger kan du associera behörigheter med en roll i organisationen. Detta skikt gör det lättare att se till att människor bara har de behörigheter som krävs för att utföra sina uppgifter.

- [Enterprise Security Package konfigurationer med Azure Active Directory (Azure AD) Domain Services i HDInsight](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- [Översikt över företags säkerhet i Azure HDInsight](domain-joined/hdinsight-security-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: för Azure HDInsight-kluster som lagrar eller bearbetar känslig information markerar du klustret och relaterade resurser som känsliga med taggar. Funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure Storage-eller beräknings resurser. Implementera en lösning från tredje part om det krävs för efterlevnad.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: om du använder Azure SQL Database för att lagra Apache Hive och Apache Oozie-metadata bör du se till att SQL-data förblir krypterade. För Azure Storage konton och Data Lake Storage (gen1 eller Gen2) rekommenderas det att låta Microsoft hantera dina krypterings nycklar, men du har möjlighet att hantera dina egna nycklar.

- [Hantera krypterings nycklar för Azure Storage konton](../storage/common/customer-managed-keys-configure-key-vault.md)

- [Så här skapar du Azure Data Lake Storage med Kundhanterade krypterings nycklar](../data-lake-store/data-lake-store-get-started-portal.md)

- [Förstå kryptering för Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md#data-encryption)

- [Så här konfigurerar du transparent datakryptering för SQL Database med Kundhanterade nycklar](../azure-sql/database/transparent-data-encryption-tde-overview.md?tabs=azure-portal)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Konfigurera diagnostikinställningar för Azure Storage konton som är kopplade till Azure HDInsight-kluster för att övervaka och logga alla CRUD-åtgärder mot kluster data. Aktivera granskning för alla lagrings konton eller Data Lake lager som är associerade med Azure HDInsight-klustret.

- [Så här aktiverar du ytterligare loggning/granskning för ett Azure Storage konto](../storage/common/manage-storage-analytics-logs.md)

- [Så här aktiverar du ytterligare loggning/granskning för Azure Data Lake Storage](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security benchmark: sårbarhet Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: implementera en sårbarhets hanterings lösning från tredje part.

Om du har en Rapid7, Qualys eller någon annan plattforms prenumeration för sårbarhets hantering kan du använda skript åtgärder för att installera sårbarhets bedömnings agenter på dina Azure HDInsight-klusternoder och hantera noderna via respektive Portal.

- [Så här installerar du Rapid7-agenten manuellt](https://insightvm.help.rapid7.com/docs/install)

- [Så här installerar du Qualys-agenten manuellt](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

- [Så här använder du skript åtgärder](hdinsight-hadoop-customize-cluster-linux.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: automatiska system uppdateringar har Aktiver ATS för klusternoder, men du måste regelbundet starta om klusternoder för att säkerställa att uppdateringarna tillämpas.

Microsoft för att underhålla och uppdatera grundläggande Azure HDInsight-Node-avbildningar.

- [Konfigurera operativ systemets uppdaterings schema för HDInsight-kluster](hdinsight-os-patching.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Distribuera automatiserad korrigerings hanterings lösning för program varu titlar från tredje part

**Vägledning**: Använd skript åtgärder eller andra mekanismer för att korrigera dina Azure HDInsight-kluster. Nya kluster kommer alltid att ha de senaste tillgängliga uppdateringarna, inklusive de senaste säkerhets korrigeringarna.

- [Konfigurera operativ systemets uppdaterings schema för Linux-baserade Azure HDInsight-kluster](hdinsight-os-patching.md)

- [Så här använder du skript åtgärder](hdinsight-hadoop-customize-cluster-linux.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: implementera en lösning för sårbarhets hantering från tredje part som har möjlighet att jämföra sårbara genomsökningar över tid. Om du har en Rapid7-eller Qualys-prenumeration kan du använda leverantörens Portal för att visa och jämföra genomsökningar med säkerhets risker från säkerhets kopiering.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: Använd ett gemensamt risk bedömnings program (t. ex. vanliga sårbarhets bedömnings system) eller standard risk klassificeringarna som tillhandahålls av genomsöknings verktyget från tredje part.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga efter och identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk, portar och protokoll osv.), inklusive Azure HDInsight-kluster, i dina prenumerationer. Se till att du har rätt (Läs) behörigheter i din klient och kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via Azure Resource Graph, rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser som går framåt.

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser som ger metadata till att logiskt organisera dem i en taxonomi.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer, vid behov, för att organisera och spåra till gångar. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla inventering av godkända Azure-resurser

**Vägledning**: definiera listan över godkända Azure-resurser och godkänd program vara för dina beräknings resurser

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Använd Azure Resource Graph för att fråga efter och identifiera resurser i dina prenumerationer. Se till att alla Azure-resurser som finns i miljön är godkända.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: implementera en lösning från tredje part för att övervaka klusternoder för program som inte är godkända.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: Använd Azure Resource Graph för att fråga efter och identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk, portar och protokoll osv.), inklusive Azure HDInsight-kluster, i dina prenumerationer.  Ta bort alla ej godkända Azure-resurser som du identifierar. För Azure HDInsight-klusternoder implementerar du en lösning från tredje part för att ta bort eller Avisera om ej godkänd program vara.

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: för Azure HDInsight-klusternoder implementerar du en lösning från tredje part för att förhindra att obehöriga program körs.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:
- Otillåtna resurstyper

- Tillåtna resurstyper

Läs mer i följande referenser:

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: underhåll en inventering av godkända program varu titlar

**Vägledning**: för Azure HDInsight-klusternoder implementerar du en lösning från tredje part för att förhindra att obehöriga filtyper körs.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security benchmark: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. HDInsight" för att skapa anpassade principer för att granska eller tillämpa nätverks konfigurationen för ditt HDInsight-kluster.

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

**Vägledning**: Azure HDInsight operativ Systems avbildningar som hanteras och underhålls av Microsoft. Kund som ansvarar för att implementera säkra konfigurationer för klusternodernas operativ system.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns] för att genomdriva säkra inställningar för dina Azure HDInsight-kluster och relaterade resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: Azure HDInsight operativ Systems avbildningar som hanteras och underhålls av Microsoft. Kund som ansvarar för att implementera tillstånds konfigurationen på operativ system nivå.

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: om du använder anpassade Azure policys definitioner använder du Azure DevOps eller Azure databaser för att lagra och hantera din kod på ett säkert sätt.

- [Azure databaser git-självstudie](/azure/devops/repos/git/gitworkflow)

- [Dokumentation om Azure databaser](/azure/devops/repos/index)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. HDInsight" för att skapa anpassade principer för avisering, granskning och system konfiguration. Dessutom kan du utveckla en process och pipeline för att hantera princip undantag.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Distribuera konfigurations hanterings verktyg för operativ system

**Vägledning**: implementera en lösning från tredje part som upprätthåller önskat tillstånd för dina kluster Node-operativsystem.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. HDInsight" för att skapa anpassade principer för granskning eller framtvinga konfigurationen av ditt HDInsight-kluster.

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: implementera en lösning från tredje part för att övervaka status för dina kluster Node-operativsystem.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Azure HDInsight innehåller Bring Your Own Key (BYOK) stöd för Apache Kafka. Med den här funktionen kan du äga och hantera nycklarna som används för att kryptera data i vila.

Alla hanterade diskar i Azure HDInsight skyddas med Azure Storage Service Encryption (SSE). Som standard krypteras data på dessa diskar med hjälp av Microsoft-hanterade nycklar. Om du aktiverar BYOK anger du krypterings nyckeln för Azure HDInsight för att använda och hantera den med hjälp av Azure Key Vault.

Key Vault kan också användas med Azure HDInsight-distributioner för att hantera nycklar för kluster lagring (Azure Storage-konton och Azure Data Lake Storage)

- [Så här hämtar du din egen nyckel för Apache Kafka på Azure HDInsight](./disk-encryption.md)

- [Hantera krypterings nycklar för Azure Storage konton](../storage/common/customer-managed-keys-configure-key-vault.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: hanterade identiteter kan användas i Azure HDInsight för att tillåta att klustren kommer åt Azure Active Directory (Azure AD) domän tjänster, åtkomst Azure Key Vault eller åtkomst till filer i Azure Data Lake Storage Gen2.

- [Förstå hanterade identiteter med Azure HDInsight](hdinsight-managed-identities.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: om du använder en kod som är relaterad till din Azure HDInsight-distribution kan du implementera autentiseringsuppgifterna för autentisering för att identifiera autentiseringsuppgifter i koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault. 

- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security benchmark: skydd mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Använd en centralt hanterad program vara mot skadlig kod

**Vägledning**: Azure HDInsight levereras med clamscan förinstallerat och aktiverat för klusternoderna, men du måste hantera program varan och manuellt aggregera/övervaka alla loggar clamscan som skapas.

- [Förstå clamscan för Azure HDInsight](./hdinsight-faq.md#security-and-certificates)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Microsoft Antimalware är aktiverat på den underliggande värden som har stöd för Azure-tjänster, men det körs inte på kund innehåll.

Genomsök alla filer som laddas upp till Azure-resurser som är relaterade till Azure HDInsight-klustrets distribution, till exempel Data Lake Storage, Blob Storage osv. Microsoft kan inte komma åt kund information i dessa instanser.

- [Förstå Microsoft Antimalware för Azure Cloud Services och Virtual Machines](../security/fundamentals/antimalware.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för skadlig program vara uppdateras

**Vägledning**: Azure HDInsight levereras med clamscan förinstallerat och aktiverat för klusternod avbildningar. Clamscan kommer att utföra motor-och definitions uppdateringar automatiskt, men agg regering och hantering av loggar måste utföras manuellt.

- [Förstå clamscan för Azure Azure HDInsight](./hdinsight-faq.md#security-and-certificates)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Azure Security benchmark: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Säkerställ regelbunden automatisk säkerhets kopiering

**Vägledning**: när du använder ett Azure Storage konto för data lagret HDInsight-kluster väljer du lämpligt alternativ för REDUNDANS (LRS, ZRS, GRS, RA-GRS).  När du använder ett Azure SQL Database för Azure HDInsight-klustrets data lager konfigurerar du aktiv geo-replikering.

- [Så här konfigurerar du redundans för Azure Storage konton](../storage/common/storage-redundancy.md)

- [Så här konfigurerar du redundans för Azure SQL Database](../azure-sql/database/active-geo-replication-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: när du använder ett Azure Storage-konto för Azure HDInsight-klustrets data lager väljer du lämpligt alternativ för REDUNDANS (LRS, ZRS, GRS, RA-GRS). Om du använder Azure Key Vault för någon del av din Azure HDInsight-distribution kontrollerar du att nycklarna säkerhets kopie ras.

- [Välj lagrings alternativ för ditt Azure HDInsight-kluster](hdinsight-hadoop-compare-storage-options.md)

- [Så här konfigurerar du redundans för Azure Storage konton](../storage/common/storage-redundancy.md)

- [Säkerhetskopiera Key Vault nycklar i Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: om Azure Key Vault används med din Azure HDInsight-distribution kan du testa att återställa Kundhanterade nycklar.

- [Så här hämtar du din egen nyckel för Apache Kafka på Azure HDInsight](./disk-encryption.md)

- [Återställa Key Vault-nycklar i Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: om Azure Key Vault används med din Azure HDInsight-distribution aktiverar du mjuk borttagning i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.

- [Så här aktiverar du mjuk borttagnings Azure Key Vault](../key-vault/general/soft-delete-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: utveckla en incident svars guide för din organisation. Se till att det finns skriftliga svars planer för incidenter som definierar alla roller för personal samt de olika faserna av incident hantering och hantering från identifiering till granskning efter incidenten. 

- [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Använd NIST hanterings guide för dator säkerhet för att hjälpa till med att skapa din egen incident svars plan](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till aviseringar, som hjälper dig att prioritera i vilken ordning du deltar i varje avisering, så att när en resurs komprometteras kan du komma åt den direkt. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller analysera den som används för att utfärda aviseringen samt den konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems incident svars funktioner på en vanlig takt. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [Se NIST: guide för test, utbildning och övnings program för IT-planer och funktioner](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en olagligt eller obehörig part.

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

- Läs mer i [Översikten över Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)
