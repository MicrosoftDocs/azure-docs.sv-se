---
title: Azures säkerhets bas linje för Virtual Network
description: Virtual Network säkerhets bas linje ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: virtual-network
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 420cd4a5b39f7c5864cf9802d02643e23c8ad24e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101666566"
---
# <a name="azure-security-baseline-for-virtual-network"></a>Azures säkerhets bas linje för Virtual Network

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark version 1,0](../security/benchmarks/overview-v1.md) till Azure Virtual Network. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure. Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för Azure Virtual Network. **Kontroller** som inte gäller för Azure Virtual Network har uteslutits.

Om du vill se hur Azure Virtual Network fullständigt mappar till Azures säkerhets benchmark kan du läsa mer i den [fullständiga Azure Virtual Network säkerhets bas linje mappnings filen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets principen för Azure-säkerhet: nätverks säkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverks gränssnitt

**Vägledning**: Använd Security Center och följ rekommendationer för nätverks skydd för att skydda dina nätverks resurser i Azure. 

Skicka flödes loggar för nätverks säkerhets grupper till en Log Analytics arbets yta och Använd Trafikanalys för att ge insikter i trafikflödet i Azure-molnet. Trafikanalys ger möjlighet att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem. 

Använd Azure Monitor loggar för att ge insikter om din miljö. En arbets yta ska användas för att sortera och analysera data och kan integreras med andra Azure-tjänster, till exempel Application Insights och Security Center. 

Välj resurs loggar som ska skickas till ett Azure Storage-konto eller en Event Hub. En annan plattform kan också användas för att analysera loggarna. 

- [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

- [Förstå nätverks säkerhet som tillhandahålls av Security Center](../security-center/security-center-network-recommendations.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Aktivera DDoS-standardskydd (distributed denial of Service) på Azure Virtual Network för att skydda mot DDoS-attacker.

Distribuera Azure-brandväggen på var och en av organisationens nätverks gränser med hot intelligens-baserad filtrering aktive rad och konfigurerad för "varning och neka" för skadlig nätverks trafik.

Använd Security Center hot skydds funktioner för att identifiera kommunikation med kända skadliga IP-adresser.

Använd Security Center anpassade nätverks säkerhets grupps rekommendationer för konfigurationer för nätverks säkerhets grupper som begränsar portar och käll-IP-adresser baserat på faktisk trafik och hot information. 

- [Hantera Azure DDoS Protection standard med hjälp av Azure Portal](../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall Threat Intelligence-baserad filtrering](../firewall/threat-intel.md)

- [Skydd mot hot i Security Center](../security-center/azure-defender.md)

- [Anpassad nätverks härdning i Azure Security Center](../security-center/security-center-adaptive-network-hardening.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets"></a>1,5: registrera nätverks paket

**Vägledning**: Använd VPN gateways paket avbildning förutom de vanligaste verktygen för paket insamling för att registrera nätverks paket. 

Du kan också granska agentbaserade eller NVA lösningar som ger åtkomst punkts-eller nätverks synlighet via paket Broker-partner lösningar som är tillgängliga i Azure Marketplace-erbjudanden.

- [Konfigurera paket avbildningar för VPN-gatewayer](../vpn-gateway/packet-capture.md) 

- [Partner för virtuell nätverks installation](https://azure.microsoft.com/solutions/network-appliances)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera nätverksbaserade intrångs identifiering/system för skydd mot intrång (ID/IP-adresser)

**Vägledning**: Använd en Azure-brandvägg som distribueras i det virtuella nätverket med hot information aktive rad. Använd Azure Firewall Threat Intelligence-baserad filtrering för att varna eller neka trafik till och från kända skadliga IP-adresser och domäner. IP-adresserna och domänerna hämtas från Microsoft Threat Intelligence-flödet. 

Du kan också välja ett lämpligt erbjudande från Azure Marketplace som stöder ID/IP-funktioner med funktioner för nytto Last kontroll.

Distribuera den brand Väggs lösning som du väljer för var och en av organisationens nätverks gränser för att upptäcka och/eller neka skadlig trafik.

- [Så här distribuerar du Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)

- [Konfigurera aviseringar med Azure-brandväggen](../firewall/threat-intel.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: Använd Virtual Network Service-taggar för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper eller Azure-brandvägg. Service märken kan användas i stället för vissa IP-adresser när du skapar säkerhets regler. Tillåt eller neka trafiken för motsvarande tjänst genom att ange service tag-namnet (till exempel API Management) i lämpligt käll-eller mål fält för en regel. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

Använd program säkerhets grupper för att förenkla komplex säkerhets konfiguration. Med program säkerhets grupper kan du konfigurera nätverks säkerhet som ett naturligt tillägg till ett programs struktur. Detta gör att du kan gruppera virtuella datorer och definiera nätverks säkerhets principer baserat på dessa grupper.

- [Förstå och använda service märken](service-tags-overview.md)

- [Förstå och använda program säkerhets grupper](./network-security-groups-overview.md#application-security-groups)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standard säkerhetskonfigurationer för nätverks resurser med Azure policy och granska de inbyggda nätverks princip definitionerna för implementering.

Se standard principen för Security Center som innehåller tillgängliga säkerhets rekommendationer relaterade till dina virtuella nätverk.

Använd Azure-ritningar för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljö artefakter, till exempel Azure Resource Manager mallar, Azure-rollbaserad åtkomst kontroll (Azure RBAC) och principer, i en enda skiss definition. Azure Blueprint kan tillämpas på nya prenumerationer för finjusterad kontroll och hantering genom versions hantering. 

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy exempel för nätverk](../governance/policy/samples/built-in-policies.md#network) 

- [Så här skapar du en Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

- [Aktivera övervakning i Azure Security Center](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Security%20Center/AzureSecurityCenter.json)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för nätverks säkerhets grupper och andra resurser som rör nätverks säkerhets-och trafikflödet. Använd fältet Beskrivning för att ange affärs behov, varaktighet och annan information för regler som tillåter trafik till/från ett nätverk för enskilda regler för nätverks säkerhets grupper.
Använd någon av de inbyggda Azure Policy definitionerna som är relaterade till taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och meddela dig om befintliga otaggade resurser.

Välj Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

- [Så här skapar du en Virtual Network](quick-create-portal.md)

- [Så här skapar du en NSG med en säkerhets konfiguration](tutorial-filter-network-traffic.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka datorkonfigurationer och identifiera ändringar i det virtuella nätverket. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska resurser sker.

- [Visa och hämta Azure aktivitets logg händelser](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets benchmark för Azure: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: Aktivera Azure Monitor för åtkomst till dina gransknings-och aktivitets loggar som innehåller händelse källa, datum, användare, tidsstämpel, käll adresser, mål adresser och andra användbara element. 

I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda Azure Storage konton för långsiktig/arkivera lagring.
Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM från tredje part. 

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Visa och hämta Azure aktivitets logg händelser](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Aktivera Azure Monitor för åtkomst till dina gransknings-och aktivitets loggar som innehåller händelse källa, datum, användare, tidsstämpel, käll adresser, mål adresser och andra användbara element.

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Visa och hämta Azure aktivitets logg händelser](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: i Azure Monitor ställer du in Log Analytics arbets ytans kvarhållningsperiod enligt organisationens regler för efterlevnad. Använd Azure Storage konton för långsiktig/arkivera lagring av säkerhets logg lagrings kvarhållning.

- [Ändra data lagrings perioden i Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Konfigurera bevarande princip för Azure Storage konto loggar](../storage/common/manage-storage-analytics-logs.md#configure-logging)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: analysera och övervaka loggar för avvikande beteende och granska resultaten regelbundet. Använd Azure Monitor Log Analytics arbets ytan för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring. 

Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM från tredje part. 

- [Förstå Log Analytics arbets yta](../azure-monitor/logs/log-analytics-tutorial.md)

- [Så här utför du anpassade frågor i Azure Monitor](../azure-monitor/logs/get-started-queries.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Komma igång med Azure Monitor och SIEM-integrering från tredje part](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: Använd Security Center med Log Analytics arbets yta för övervakning och avisering om avvikande aktivitet i säkerhets loggar och händelser.

Alternativt kan du aktivera och publicera data till Azure Sentinel eller från en SIEM för aviseringar.

- [Hantera aviseringar i Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Så här aviserar du om Log Analytics-loggdata](../azure-monitor/alerts/tutorial-response.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: implementera en lösning från tredje part från Azure Marketplace för DNS-loggning av lösningen enligt organisationens behov.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azures säkerhets benchmark: identitets-och åtkomst kontroll](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: Använd Azure Active Directory (Azure AD) inbyggda administratörs roller som kan tilldelas explicit och som kan frågas. 

Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

- [Så här hämtar du en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton. Använd Security Center identitets-och åtkomst hantering för att övervaka antalet administrativa konton.

Aktivera just-in-Time/just-Access genom att använda Azure AD Privileged Identity Management privilegierade roller för Microsoft-tjänster och Azure Resource Manager. 

- [Läs mer om Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Använd Azure Active Directory enkel inloggning (SSO)

**Vägledning**: Använd SSO med Azure Active Directory (Azure AD) i stället för att konfigurera enskilda fristående autentiseringsuppgifter per tjänst. Använd Security Centers rekommendationer för identitets-och åtkomst hantering.

- [Enkel inloggning till program i Azure Active Directory](../active-directory/manage-apps/what-is-single-sign-on.md) 

- [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory-baserad åtkomst

**Vägledning**: Aktivera Azure Active Directory (Azure AD) Multi-Factor Authentication (MFA) och följ Security Centers rekommendationer för identitets-och åtkomst hantering.

- [Aktivera MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Använd säkra, Azure-hanterade arbets stationer för administrativa uppgifter

**Vägledning**: Använd Paw (Privileged Access Workstation) med Multi-Factor Authentication (MFA) som kon figurer ATS för att logga in på och komma åt Azures nätverks resurser.

- [Lär dig mer om arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Aktivera MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: använda Azure Active Directory (Azure AD) risk identifiering för att visa aviseringar och rapporter om riskfyllda användar beteenden. 

Hämta Security Center risk identifierings aviseringar till Azure Monitor och konfigurera anpassade aviseringar/aviseringar med hjälp av åtgärds grupper.

- [Förstå Security Center risk identifieringar (misstänkt aktivitet)](../active-directory/identity-protection/overview-identity-protection.md) 

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 

- [Konfigurera åtgärds grupper för anpassad avisering och avisering](../azure-monitor/alerts/action-groups.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Använd villkorlig åtkomst med namngivna platser för att tillåta åtkomst från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner.

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: använda Azure Active Directory (Azure AD) som ett centralt system för autentisering och auktorisering för dina tjänster. Azure AD skyddar data med stark kryptering för data i vila och under överföring och även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.  

- [Skapa och konfigurera en Azure AD-instans](../active-directory-domain-services/tutorial-create-instance.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Använd Azure Active Directory (Azure AD) för att tillhandahålla loggar för att identifiera inaktuella konton. 

Granskningar av Azure Identity Access kan utföras för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användar åtkomsten bör granskas regelbundet för att se till att endast aktiva användare har fortsatt åtkomst.

- [Förstå Azure AD repor ting](../active-directory/reports-monitoring/index.yml)

- [Så här använder du granskningar av Azure Identity Access](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: integrera Azure Active Directory (Azure AD) inloggnings aktivitet, gransknings-och risk händelse logg källor med valfritt Siem eller övervaknings verktyg baserat på din åtkomst. 

Effektivisera den här processen genom att skapa diagnostikinställningar för Azure Active Directory användar konton och skicka gransknings loggar och inloggnings loggar till en Log Analytics arbets yta. Alla önskade aviseringar kan konfigureras i Log Analytics arbets ytan.

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: varning vid inloggnings beteende för konto

**Vägledning**: Använd Azure Active Directorys (Azure AD)-risk-och identitets skydds funktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter för ditt virtuella nätverk. Mata in data i Azure Sentinel för ytterligare undersökningar.

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security benchmark: Data Protection](../security/benchmarks/security-control-data-protection.md).*

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: kryptera all känslig information under överföring. Se till att alla klienter som ansluter till dina Azure-resurser i dina virtuella nätverk kan förhandla TLS 1,2 eller senare. Följ Security Center rekommendationer för kryptering i vila och kryptering under överföring. 

Microsoft tillhandahåller flera alternativ som kan användas av kunder för att skydda data i överföring internt i Azure-nätverket och externt via Internet till slutanvändaren. Dessa inkluderar kommunikation via virtuella privata nätverk (användning av IPsec/IKE-kryptering), Transport Layer Security (TLS) 1,2 eller senare (via Azure-komponenter som Application Gateway eller Azure-frontend), protokoll direkt på de virtuella Azure-datorerna (t. ex. Windows IPsec eller SMB) med mera.

Dessutom är "kryptering som standard" med MACsec (en IEEE-standard på data länk skiktet) aktive rad för all Azure-trafik mellan Azure-datacenter för att säkerställa konfidentialitet och integritet hos kunddata.

- [Förstå kryptering i överföring med Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Använd Azure RBAC för att hantera åtkomst till resurser 

**Vägledning**: Använd rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att hantera åtkomst till data och resurser. Använd annars tjänstspecifika åtkomst kontroll metoder. 

Välj inbyggda roller som ägare, deltagare eller nätverks deltagare och tilldela rollen till lämplig omfattning. Du kan till exempel tilldela en delmängd av virtuella nätverksfunktioner med de behörigheter som krävs för virtuella nätverk till någon av dessa roller. 

- [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Planera virtuella nätverk](virtual-network-vnet-plan-design-arm.md#permissions)

- [Granska de inbyggda Azure-rollerna](../role-based-access-control/built-in-roles.md#networking)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets loggar för att skapa aviseringar för när ändringar sker i kritiska Azure-resurser som virtuella nätverk och nätverks säkerhets grupper.

- [Diagnostisk loggning för en nätverks säkerhets grupp](virtual-network-nsg-manage-log.md)

- [Så här skapar du aviseringar för Azure aktivitets logg händelser](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga och identifiera alla nätverks resurser som virtuella nätverk, undernät i dina prenumerationer. Se till att du har rätt (Läs) behörigheter i din klient och kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md) 

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0) 

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser som ger metadata till att logiskt organisera dem i en taxonomi.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer, vid behov, för att organisera och spåra virtuella nätverk och relaterade resurser. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md) 

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md) 

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla inventering av godkända Azure-resurser

**Vägledning**: du måste skapa en inventering av godkända Azure-resurser och godkänd program vara för beräknings resurser enligt organisationens behov.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:
- Otillåtna resurstyper 

- Tillåtna resurstyper 

Fråga eller identifiera resurser i prenumerationer med Azures resurs diagram i miljöer med hög säkerhet, till exempel de med Azure Storage-konton. 

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Exempel på inbyggda Azure policy-moduler för virtuellt nätverk](./policy-reference.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: förhindra att resurser skapas eller används med Azure policy som krävs av organisationens principer. Implementera processer för borttagning av obehöriga resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:
- Otillåtna resurstyper 

- Tillåtna resurstyper 

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/index.md)

- [Exempel på inbyggda Azure policy-moduler för virtuellt nätverk](./policy-reference.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd villkorlig åtkomst i Azure för att begränsa användarens möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security benchmark: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: Använd Azure policy alias för att skapa anpassade principer för att granska eller tillämpa konfigurationen av dina Azure-nätverks resurser och även använda inbyggda Azure policy definitioner.

Exportera någon av dina Bygg mallar med Azure Resource Manager i JavaScript Object Notation (JSON) form och granska den för att säkerställa att konfigurationerna uppfyller eller överskrider organisationens säkerhets krav.

Implementera rekommendationer från Security Center som en säker konfigurations bas linje för dina Azure-resurser.

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Självstudie: skapa och hantera principer för att genomdriva efterlevnad](../governance/policy/tutorials/create-and-manage.md)

- [Exempel på inbyggda Azure policy-moduler för virtuellt nätverk](./policy-reference.md)

- [Exportera en och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Säkerhetsrekommendationer – en referensguide](../security-center/recommendations-reference.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure Resource Manager mallar och Azure-principer för att konfigurera Azure-resurser som är kopplade till det virtuella nätverket och relaterade resurser på ett säkert sätt.  Azure Resource Manager mallar är JSON-baserade (JavaScript Object Notation) filer som används för att distribuera virtuella datorer tillsammans med Azure-resurser. Microsoft utför underhållet på bas mallarna.  

Använd Azure Policy [neka] och [distribuera om det inte finns]-effekter för att framtvinga säkra inställningar i dina Azure-resurser.

- [Information om hur du skapar Azure Resource Manager mallar](../virtual-machines/windows/ps-template.md) 

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

- [Azure Resource Manager-exempelmallar för virtuella nätverk](template-samples.md)

- [Exempel på inbyggda Azure policy-moduler för virtuellt nätverk](./policy-reference.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: Använd Azure DevOps för att lagra och hantera din kod på ett säkert sätt, t. ex. anpassade Azure-principer, Azure Resource Manager mallar och önskade tillstånds konfigurations skript. och så vidare.

Du måste ha behörighet att komma åt de resurser som du vill hantera i Azure DevOps, t. ex. kod, versioner och arbets uppföljning. De flesta behörigheter beviljas via inbyggda säkerhets grupper. Du kan bevilja eller neka behörigheter till vissa användare, inbyggda säkerhets grupper eller grupper som definierats i Azure Active Directory (Azure AD) om det är integrerat med Azure DevOps eller Active Directory om det är integrerat med Team Foundation Server.

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [Om behörigheter och grupper i Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för Azure-resurser med hjälp av Azure policy. Använd Azure Policy alias för att skapa anpassade principer för att granska eller tillämpa nätverks konfigurationen för dina Azure-resurser och eventuella inbyggda princip definitioner relaterade till vissa resurser. 

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Använda alias](../governance/policy/concepts/definition-structure.md#aliases)

- [Exempel på inbyggda Azure policy-moduler för virtuellt nätverk](./policy-reference.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: Använd Security Center för att utföra bas linjes ökningar för Azure Virtual Network och relaterade resurser. Använd Azure Policy för att varna och granska Azure-resursfiler.

- [Så här åtgärdar du rekommendationer i Security Center](../security-center/security-center-remediate-recommendations.md)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Exempel på inbyggda Azure policy-moduler för virtuellt nätverk](./policy-reference.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Använd hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda hemlig hantering för dina Azure-resurser som finns i en azure-Virtual Network.

- [Integrera med Azure Managed Identities](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Så här skapar du en Key Vault](../key-vault/secrets/quick-create-portal.md) 

- [Så här ger Key Vault autentisering med en hanterad identitet](../key-vault/general/assign-access-policy-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault.

- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Azure Security benchmark: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Säkerställ regelbunden automatisk säkerhets kopiering

**Vägledning**: Använd Azure Resource Manager för att distribuera ett virtuellt nätverk och relaterade resurser. Azure Resource Manager ger möjlighet att exportera mallar som kan användas som säkerhets kopior för att återställa virtuella nätverk och relaterade resurser.  Använd Azure Automation för att anropa export-API: t för Azure Resource Manager Template regelbundet.

- [Översikt över Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Azure Resource Manager-exempelmallar för virtuella nätverk](template-samples.md)

- [Exportera en och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Resurs grupper – exportera mall](/rest/api/resources/resourcegroups/exporttemplate)

- [Introduktion till Azure Automation](../automation/automation-intro.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: Använd Azure Resource Manager för att distribuera ett virtuellt nätverk och relaterade resurser. Azure Resource Manager ger möjlighet att exportera mallar som kan användas som säkerhets kopior för att återställa virtuella nätverk och relaterade resurser. Använd Azure Automation för att anropa export-API: t för Azure Resource Manager Template regelbundet. Säkerhetskopiera Kundhanterade nycklar inom Azure Key Vault.

- [Översikt över Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Azure Resource Manager-exempelmallar för virtuella nätverk](template-samples.md)

- [Exportera en och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Resurs grupper – exportera mall](/rest/api/resources/resourcegroups/exporttemplate)

- [Introduktion till Azure Automation](../automation/automation-intro.md)

- [Så här säkerhetskopierar du nyckel valv nycklar i Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: utför regelbundet distribution av Azure Resource Manager mallar till en isolerad prenumeration och testa återställning av säkerhetskopierade nycklar som hanteras av kunden.

- [Distribuera resurser med ARM-mallar och Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: Använd Azure-DevOps för att lagra och hantera din kod på ett säkert sätt, t. ex. anpassade Azure policy definitioner och Azure Resource Manager mallar. 

Bevilja eller neka behörigheter till vissa användare, inbyggda säkerhets grupper eller grupper som definierats i Azure Active Directory (Azure AD) om det är integrerat med Azure DevOps eller Active Directory om det är integrerat med Team Foundation Server.  

Använd rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att skydda Kundhanterade nycklar.   

Aktivera Soft-Delete och rensa skydd i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.  

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Om behörigheter och grupper i Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Aktivera Soft-Delete och rensa skydd i Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal) 

- [Mjuk borttagning för Azure Storage-blobar](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security benchmark: incident svar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.  

- [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Utnyttja NISTs hanterings guide för dator säkerhet för att hjälpa dig att skapa en egen incident svars plan](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Markera prenumerationer (till exempel produktion eller icke-produktion) med taggar och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser, särskilt för bearbetning av känsliga data.  Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhets aviseringar i Security Center](../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems funktioner för incident svar på en vanlig takt för att hjälpa till att skydda dina Azure-resurser. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [NIST-guide för att testa, träna och träna program för IT-planer och funktioner](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en olagligt eller obehörig part. Granska incidenter när du är säker på att problemen är lösta.

- [Så här ställer du in Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export för att identifiera risker för Azure-resurser. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. 

Du kan också använda Security Center Data Connector för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för arbets flödes automatisering i Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer för att skydda dina Azure-resurser.

- [Konfigurera automatisering av arbets flöden och Logic Apps](../security-center/workflow-automation.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstester och Red Team-tester

*Mer information finns i [övningen för Azure Security benchmark: inträngande tester och röda team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program.

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)